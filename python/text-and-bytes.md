# 文本和字节

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

> 在`Windows`系统中常常会因为编码的混乱而出现不可预知的错误，所以要尽量指定读写文件的明确编码

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

# BEGIN SHAVE_MARKS
import unicodedata
import string


def shave_marks(txt):
    """Remove all diacritic marks"""
    norm_txt = unicodedata.normalize('NFD', txt)  # <1>
    shaved = ''.join(c for c in norm_txt
                     if not unicodedata.combining(c))  # <2>
    return unicodedata.normalize('NFC', shaved)  # <3>


# END SHAVE_MARKS

# BEGIN SHAVE_MARKS_LATIN
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


# END SHAVE_MARKS_LATIN

# BEGIN ASCIIZE
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
# END ASCIIZE
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

Last Modified 2022-07-08
