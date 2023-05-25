# 文本和字节

字符的标识，即码点，是`0~1,114,111`范围内的数（十进制），在 Unicode 标准中以`4~6`个十六进制数表示，前加`U+`，取值范围是`U+0000~U+10FFFF`。例如，字母 A 的码
点是`U+0041`，欧元符号的码点是`U+20AC`，音乐中高音谱号的码点是`U+1D11E`。

字符的具体表述取决于所用的编码。编码是在码点和字节序列之间转换时使用的算法。例如，字母A（U+0041）在 UTF-8 编码中使用单个字节`\x41`表述，而在 UTF-16LE 编码中使用字节序列`\x41\x00`表述。再比如，欧元符号（U+20AC）在 UTF-8 编码中需要3个字节，即`\xe2\x82\xac`，而在 UTF-16LE 中，同一个码点编码成两个字节，即`\xac\x20`。

把码点转换成字节序列的过程叫编码，把字节序列转换成码点的过程叫解码。

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

city = 'São Paulo'
print(city.encode('utf-8'))
print(city.encode('utf-16'))
print(city.encode('iso8859-1'))
# 忽略无法编码的字符
print(city.encode('cp437', errors='ignore'))
# 替换无法编码的字符为 ?
print(city.encode('cp437', errors='replace'))
# 替换无法编码的字符为 xml 表示的实体字符
print(city.encode('cp437', errors='xmlcharrefreplace'))

octets = b'Montr\xe9al'
print(octets.decode('cp1252'))
print(octets.decode('iso8859-7'))
print(octets.decode('koi8-r'))
# UTF-8 中对于未知字符有官方指定的字符 � (码位 U+FFFD) 来表示
print(octets.decode('utf-8', errors='replace'))

# 前面会有表示 Intel CPU 小字节序的 BOM 前缀 b'\xff\xfe'
u16 = 'El Niño'.encode('utf-16')
print(u16, list(u16))
# 直接指明使用小字节序
u16le = 'El Niño'.encode('utf-16le')
print(u16le, list(u16le))
# 直接指明使用大字节序
u16be = 'El Niño'.encode('utf-16be')
print(u16be, list(u16be))
```

```python
import array
numbers = array.array('h', [-2, -1, 0, 1, 2])
octets = bytes(numbers)
print(octets)
```

```python
import locale
import sys

expressions = """
        locale.getpreferredencoding()
        type(my_file)
        my_file.encoding
        sys.stdout.isatty()
        sys.stdout.encoding
        sys.stdin.isatty()
        sys.stdin.encoding
        sys.stderr.isatty()
        sys.stderr.encoding
        sys.getdefaultencoding()
        sys.getfilesystemencoding()
    """

my_file = open('dummy', 'w')

for expression in expressions.split():
    value = eval(expression)
    print(f'{expression:>30} -> {value!r}')

my_file.close()
```

>在`Windows`系统中常常会因为编码的混乱而出现不可预知的错误，所以要尽量指定读写文件的明确编码

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

## Unicode 规范化比较

```python
"""
Utility functions for normalized Unicode string comparison.

Using Normal Form C, case sensitive:

    >>> s1 = 'café'
    >>> s2 = 'cafe\u0301'
    >>> s1 == s2
    False
    >>> nfc_equal(s1, s2)
    True
    >>> nfc_equal('A', 'a')
    False

Using Normal Form C with case folding:

    >>> s3 = 'Straße'
    >>> s4 = 'strasse'
    >>> s3 == s4
    False
    >>> nfc_equal(s3, s4)
    False
    >>> fold_equal(s3, s4)
    True
    >>> fold_equal(s1, s2)
    True
    >>> fold_equal('A', 'a')
    True

"""

from unicodedata import normalize


def nfc_equal(str1, str2):
    return normalize('NFC', str1) == normalize('NFC', str2)


def fold_equal(str1, str2):
    return (normalize('NFC', str1).casefold() ==
            normalize('NFC', str2).casefold())
```

## 去除变音字符

```python
"""
Radical folding and text sanitizing.

Handling a string with `cp1252` symbols:

    >>> order = '“Herr Voß: • ½ cup of Œtker™ caffè latte • bowl of açaí.”'
    >>> shave_marks(order)
    '“Herr Voß: • ½ cup of Œtker™ caffe latte • bowl of acai.”'
    >>> shave_marks_latin(order)
    '“Herr Voß: • ½ cup of Œtker™ caffe latte • bowl of acai.”'
    >>> dewinize(order)
    '"Herr Voß: - ½ cup of OEtker(TM) caffè latte - bowl of açaí."'
    >>> asciize(order)
    '"Herr Voss: - 1⁄2 cup of OEtker(TM) caffe latte - bowl of acai."'

Handling a string with Greek and Latin accented characters:

    >>> greek = 'Ζέφυρος, Zéfiro'
    >>> shave_marks(greek)
    'Ζεφυρος, Zefiro'
    >>> shave_marks_latin(greek)
    'Ζέφυρος, Zefiro'
    >>> dewinize(greek)
    'Ζέφυρος, Zéfiro'
    >>> asciize(greek)
    'Ζέφυρος, Zefiro'

"""

import unicodedata
import string


def shave_marks(txt):
    """Remove all diacritic marks"""
    norm_txt = unicodedata.normalize('NFD', txt)  # <1>
    shaved = ''.join(c for c in norm_txt
                     if not unicodedata.combining(c))  # <2>
    return unicodedata.normalize('NFC', shaved)  # <3>


def shave_marks_latin(txt):
    """Remove all diacritic marks from Latin base characters"""
    norm_txt = unicodedata.normalize('NFD', txt)  # <1>
    latin_base = False
    keepers = []
    for c in norm_txt:
        if unicodedata.combining(c) and latin_base:  # <2>
            continue  # ignore diacritic on Latin base char
        keepers.append(c)  # <3>
        # if it isn't combining char, it's a new base char
        if not unicodedata.combining(c):  # <4>
            latin_base = c in string.ascii_letters
    shaved = ''.join(keepers)
    return unicodedata.normalize('NFC', shaved)  # <5>


single_map = str.maketrans("""‚ƒ„†ˆ‹‘’“”•–—˜›""",  # <1>
                           """'f"*^<''""---~>""")

multi_map = str.maketrans({  # <2>
    '€': '<euro>',
    '…': '...',
    'Œ': 'OE',
    '™': '(TM)',
    'œ': 'oe',
    '‰': '<per mille>',
    '‡': '**',
})

multi_map.update(single_map)  # <3>


def dewinize(txt):
    """Replace Win1252 symbols with ASCII chars or sequences"""
    return txt.translate(multi_map)  # <4>


def asciize(txt):
    no_marks = shave_marks_latin(dewinize(txt))  # <5>
    no_marks = no_marks.replace('ß', 'ss')  # <6>
    return unicodedata.normalize('NFKC', no_marks)  # <7>
```

## Unicode 排序

```python
import pyuca

coll = pyuca.Collator()
fruits = ['caju', 'atemoia', 'cajá', 'açai', 'acerola']
sorted_fruits = sorted(fruits, key=coll.sort_key)
print(sorted_fruits)
print(sorted(fruits))
```

```python
import re
import unicodedata
re_digit = re.compile(r'\d')

sample = '1\xbc\xb2\u0969\u136b\u216b\u2466\u2480\u3285'

for char in sample:
    print('U+%04X' % ord(char),
          char.center(6),
          're_dig' if re_digit.match(char) else '-',
          'isdig' if char.isdigit() else '-',
          'isnum' if char.isnumeric() else '-',
          format(unicodedata.numeric(char), '5.2f'),
          unicodedata.name(char),
          sep='\t')
```

## 字符串与字节序列双模式

双模式是指根据提供的字符串或字节序列(bytes)来展现不同的行为

```python
import re

re_numbers_str = re.compile(r'\d+')     # <1>
re_words_str = re.compile(r'\w+')
re_numbers_bytes = re.compile(rb'\d+')  # <2>
re_words_bytes = re.compile(rb'\w+')

text_str = ("Ramanujan saw \u0be7\u0bed\u0be8\u0bef"  # <3>
            " as 1729 = 1³ + 12³ = 9³ + 10³.")        # <4>

text_bytes = text_str.encode('utf_8')  # <5>

print('Text', repr(text_str), sep='\n  ')
print('Numbers')
print('  str  :', re_numbers_str.findall(text_str))      # <6>
print('  bytes:', re_numbers_bytes.findall(text_bytes))  # <7>
print('Words')
print('  str  :', re_words_str.findall(text_str))        # <8>
print('  bytes:', re_words_bytes.findall(text_bytes))    # <9>
```

```python
import os

with open(b'digits-for-\xcf\x80.txt'.decode('utf8'), 'w', encoding='utf8') as f:
    f.write('3.141592654')
with open('abc.txt', 'w', encoding='utf8') as f:
    f.write('abc')

print(os.listdir('.'))
print(os.listdir(b'.'))

pi_name_bytes = b'digits-for-\xcf\x80.txt'
# 将未知的字符转为保留的 Unicode 码位 (U+DC00到U+DCFF), 这些码位是未分配字符的
pi_name_str = pi_name_bytes.decode('ascii', 'surrogateescape')
# 由于使用了 Unicode 中的保留码位, 可能会编码失败出现异常
try:
    print(pi_name_str)
except UnicodeEncodeError as e:
    print(e)
print(pi_name_str.encode('ascii', 'surrogateescape'))
```

Last Modified 2022-07-11
