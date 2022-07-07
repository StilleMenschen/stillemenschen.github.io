# 字符串和字节

## 编码和解码

```python
a = 'café'
print(a)
b = a.encode('utf-8')
print(b)
print(len(b))

cafe = bytes('café', encoding='utf-8')
print(cafe[0])
print(cafe[:1])
cafe_arr = bytearray(cafe)
print(cafe_arr)
print(cafe_arr[0])
print(cafe_arr[-1:])

print(bytes.fromhex('31 2F 3C 20 2B'))

for codec in ['latin-1', 'utf-8', 'utf-16']:
    print(codec, 'El Niño'.encode(encoding=codec), sep='\t')
```

```python
import array
numbers = array.array('h', [-2, -1, 0, 1, 2])
octets = bytes(numbers)
print(octets)
```

## 获得图片首部

```python
import struct

fmt = '<3s3sHH'
with open('tiny.gif', 'rb') as fp:
    img = memoryview(fp.read())
header = img[:10]
print(bytes(header))
print(struct.unpack(fmt, header))
del header
del img
```

Last Modified 2022-07-07
