# 加解密参考

```bash
pip install pycryptodome rsa
```

```python
import base64
import hashlib
import json
import time

import rsa
from Crypto.Cipher import AES
from Crypto.Cipher import PKCS1_v1_5
from Crypto.PublicKey import RSA

BLOCK_SIZE = AES.block_size


def get_key(src_key: str) -> bytes:
    if not len(src_key) or len(src_key) > 16:
        raise '密钥必须为1-16位英文或数字字符'
    # 16 字节的密码不足向后补位 0
    final_key = f'{src_key:016s}'
    return final_key.encode()


def get_sha1prng_key(src_key: str) -> bytes:
    """encrypt key with SHA1PRNG.
    same as java AES crypto key generator SHA1PRNG.
    SecureRandom random = SecureRandom.getInstance("SHA1PRNG");
    random.setSeed(decryptKey.getBytes());
    keygen.init(128, secureRandom);
    :param src_key: original key.
    :return bytes: encrypt key with SHA1PRNG, 128 bits or 16 long bytes.
    """
    if not len(src_key) or len(src_key) > 16:
        raise '密钥必须为1-16位英文或数字字符'
    signature: bytes = hashlib.sha1(src_key.encode()).digest()
    signature: bytes = hashlib.sha1(signature).digest()
    return signature[:16]


# fill function
def padding(data):
    """补位填充够 16 字节"""
    block = BLOCK_SIZE - len(data.encode('utf-8'))
    return data + (block % BLOCK_SIZE) * chr(block % BLOCK_SIZE)


# truncation function
def un_padding(data):
    """反补位填 16 字节"""
    return data[:-ord(data[-1])]


def parse_byte_to_hex_str(src: bytes):
    """将字节转为 16 进制字符串"""
    result = []
    for val in src:
        s = f'{(val & 0xFF):02X}'
        result.append(s)
    return ''.join(result)


def parse_hex_str_to_byte(src: str):
    """将 16 进制字符串转为字节"""
    if not len(src):
        raise 'Error: string must be input'
    b_arr = bytearray()
    for idx in range(len(src) // 2):
        high = int(src[idx * 2: idx * 2 + 1], base=16)
        low = int(src[idx * 2 + 1: idx * 2 + 2], base=16)
        b_arr.append(high * 16 + low)
    return b_arr


def get_base64(src: str):
    """基于 UTF-8 编码的 Base64 加密"""
    b_src = src.encode('utf-8')
    return base64.b64encode(b_src).decode(encoding='utf-8')


def from_base64(src: str):
    """基于 UTF-8 编码的 Base64 解密"""
    return base64.b64decode(src).decode(encoding='utf-8')


def aes_encrypt(plaintext, secret_key):
    """加密"""
    try:
        # 16 字节填充
        padding_text = padding(plaintext).encode('utf-8')
        # 初始化加密类
        # cryptor = AES.new(secret_key, AES.MODE_CBC, secret_key)
        cryptor = AES.new(secret_key, AES.MODE_ECB)
        # AES 加密
        encrypt_aes = cryptor.encrypt(padding_text)
        # Do BASE64 transcoding
        # encrypt_text = (base64.b64encode(encrypt_aes)).decode()
        # 将密文字节转为 16 进制字符串
        encrypt_text = parse_byte_to_hex_str(encrypt_aes)
        # 将字符串转为 UTF-8 加密的 Base64 字符串
        return get_base64(encrypt_text)
    except Exception as e:
        print(e)
        return None


def aes_decrypt(ciphertext, secret_key):
    """解密"""
    try:
        # 初始化加密类
        # cryptor = AES.new(secret_key, AES.MODE_CBC, secret_key)
        cryptor = AES.new(secret_key, AES.MODE_ECB)
        # Do BASE64 transcoding
        # plain_base64 = base64.b64decode(ciphertext)
        # 将 Base64 字符串转为 16 进制字符串
        b_hex = from_base64(ciphertext)
        # 将 16 进制字符串转为实际的密文字节
        plain_base64 = parse_hex_str_to_byte(b_hex)
        # 使用 AES 解密
        decrypt_text = cryptor.decrypt(plain_base64)
        # 反填充 16 字节
        plain_text = un_padding(decrypt_text.decode('utf-8'))
        return plain_text
    except Exception as e:
        print(e)
        return None


# 定义函数，用于将字符串按指定长度分割为列表
def split_string(string, length):
    return [string[i:i + length] for i in range(0, len(string), length)]


def rsa_encrypt(public_key_text, data_text):
    # public_key_text_pem = '-----BEGIN PUBLIC KEY-----\n' + public_key_text + '\n-----END PUBLIC KEY-----'
    # public_key = rsa.PublicKey.load_pkcs1_openssl_pem(public_key_text_pem.encode())

    # 将公钥文本转换为RSA公钥对象
    public_key = rsa.PublicKey.load_pkcs1(public_key_text.encode())

    '''
    在RSA加密中，加密的明文长度限制是由公钥的长度确定的。具体来说，RSA算法对明文进行加密时，会将明文分割为固定大小的块，每个块的大小由公钥的长度决定。而为了确保加密的安全性，还需要在每个块中添加一些填充数据。
    在PKCS#1标准中，用于填充的方式称为PKCS#1 v1.5填充，它将填充数据的长度至少设置为11个字节。因此，当我们计算每个加密块的大小时，需要将可用于加密的最大长度减去填充数据的长度（即11字节）。
    所以，代码中计算max_encrypt_length时的减去11的操作是为了确保每个加密块的长度满足要求，并保证加密的正确性和安全性。
    '''
    max_encrypt_length = rsa.common.byte_size(public_key.n) - 11
    encrypted_parts = []

    # 将文本按最大加密长度分割为列表
    text_parts = split_string(data_text, max_encrypt_length)

    # 逐段加密并拼接加密结果
    for part in text_parts:
        encrypted_part = rsa.encrypt(part.encode(), public_key)
        # 将字节转为 16 进制字符
        encrypted_parts.append(encrypted_part.hex())

    # 将分段加密结果拼接为一个字符串返回
    return ''.join(encrypted_parts)


def encrypt_text_with_public_key(public_key, message):
    # public_key = '-----BEGIN PUBLIC KEY-----\n' + public_key + '\n-----END PUBLIC KEY-----'
    # 将公钥导入为RSA对象
    rsa_key = RSA.importKey(public_key)

    # 创建PKCS1_v1_5加密器对象
    cipher = PKCS1_v1_5.new(rsa_key)

    # 获取公钥的长度（以字节为单位）
    key_length = rsa_key.size_in_bytes()

    # 根据密钥长度计算每个分块的大小
    max_block_size = key_length - 11  # PKCS1_v1_5算法要求每个分块少于（密钥长度-11）个字节

    encrypted_blocks = []
    message_bytes = message.encode()

    # 将消息分成较小的块并使用公钥加密
    for i in range(0, len(message_bytes), max_block_size):
        block = message_bytes[i:i + max_block_size]
        encrypted_block = cipher.encrypt(block)
        encrypted_blocks.append(encrypted_block.hex())

    # 将加密后的块连接成一个字节串
    encrypted_message = ''.join(encrypted_blocks)

    return encrypted_message


def main1():
    # 密码不足 16 位补足 16 位, AES CBC 模式
    # key = get_key('12345678')
    # 获得类似 java 的 SHA1PRNG 密钥, AES ECB 模式
    secret_key = get_sha1prng_key('12345678')
    # 加密消息
    message = aes_encrypt('Jack Mar', secret_key)
    print(message)
    # 解密消息
    msg = aes_decrypt(message, secret_key)
    print(msg)


def main2():
    public_key, private_key = rsa.newkeys(1024)
    public_key_text = public_key.save_pkcs1().decode()

    data_json = {
        'userName': 'fake',
        'creditCode': '123456789',
        'additionalInfo': {
            'expenseId': '20001',
            'processId': '100001'
        },
        'timestamp': int(time.time() * 1000),
    }
    data_text = json.dumps(data_json)

    encrypted_data_text = rsa_encrypt(public_key_text, data_text)
    print(encrypted_data_text.upper())


def main3():
    key = RSA.generate(1024)
    private_key = key.export_key()
    public_key = key.publickey().export_key()

    public_key_text = public_key

    data_json = {
        'userName': 'fake',
        'creditCode': '123456789',
        'additionalInfo': {
            'expenseId': '20001',
            'processId': '100001'
        },
        'timestamp': int(time.time() * 1000),
    }
    data_text = json.dumps(data_json)

    encrypted_data_text = encrypt_text_with_public_key(public_key_text, data_text)
    print(encrypted_data_text.upper())


if __name__ == '__main__':
    main1()
    main2()
    main3()
```

Last Modified 2024-01-30
