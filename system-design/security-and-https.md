# 安全性和HTTPS

## 中间人攻击

一种攻击方式，攻击者拦截一条被认为是其通信双方私有的通信线。如果一个恶意的行为者在从客户端到服务器的路上拦截并篡改了一个 IP 数据包，那将是一个中间人的攻击。中间人攻击是加密和 HTTPS 要抵御的主要威胁。

## 对称加密

一种仅依赖于一个密钥来加密和解密数据的加密类型。参与沟通的各方都必须知道钥匙，因此通常必须在某一点或另一点之间共享。对称密钥算法往往比非对称算法速度更快。最广泛使用的对称密钥算法是高级加密标准 (AES) 的一部分。

## 非对称加密

非对称加密也被称为公钥加密，它依赖于两个密钥，一个公钥和一个私钥，来加密和解密数据。这些密钥使用加密算法生成，并进行数学连接，这样用公钥加密的数据只能用私钥解密。虽然私钥必须保持安全，以保持这种加密标准的保真度，但公钥可以公开共享。非对称密钥算法往往比对称加密算法速度慢。

## AES

代表高级加密标准。AES 是一种被广泛使用的加密标准，它有三种对称密钥算法 (AES-128、AES-192 和 AES-256) 。值得注意的是，AES 被认为是加密领域的“黄金标准”，甚至被美国国家安全局用来加密绝密信息。

## HTTPS

超文本安全传输协议 (HyperText Transfer Protocol Secure) 是 HTTP 的一个扩展，用于在线安全通信。它要求服务器具有可信的证书 (通常是 SSL 证书) ，并使用安全传输层 (TLS) ，一种构建在 TCP 之上的安全协议，来加密客户端和服务器之间通信的数据。

## TLS

安全传输层 (Transport Layer Security) 是 HTTP 运行的一种安全协议，以实现在线安全通信。`HTTP over TLS`也被称为 HTTPS。

## SSL 证书

由证书颁发机构授予服务器的数字证书。包含服务器的公钥，将用作 HTTPS 连接中的 TLS 握手进程的一部分。SSL 证书可以有效地确认公钥属于声明其属于服务器的服务器。SSL 证书是抵御中间人攻击的关键防御手段。

## 认证授权

签署数字证书的可信实体，即 HTTPS 连接中依赖的 SSL 证书。

## TLS 握手

客户端和服务器通过 HTTPS 进行通信，交换加密相关信息并建立安全通信的过程。TLS 握手的典型步骤大致如下：

- 客户端向服务器发送一个客户端 hello，一个随机字节串。

- 服务器响应一个服务器 hello、另一个随机字节串以及包含公钥的 SSL 证书。

- 客户端验证证书是由证书颁发机构颁发的，并向服务器发送一个预主密钥，即另一个随机字节串，这次是用服务器的公钥加密的。

- 客户端和服务器使用客户端 hello、服务器 hello 和预主密钥来生成相同的对称加密会话密钥，用于加密和对在其余连接期间通信的所有数据进行解密。

## Demo

```js
const aes256 = require("aes256");

const key = " special-key-1";
const otherKey = "special-key-2";
const plaintext = " SystemsExpert is powerful!";

const encrypted = aes256.encrypt(key, plaintext);
console.log("Encrypted:", encrypted);

const decrypted = aes256.decrypt(key, encrypted);
console.log("Decrypted:", decrypted);

const failedDecrypted = aes256.decrypt(otherKey, encrypted);
console.log("Failed Decrypted:", failedDecrypted);
```

Last Modified 2022-02-24
