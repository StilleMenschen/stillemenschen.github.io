# 字典与集合

如果一个对象的哈希码在整个生命周期内永不改变（依据`__hash__()`方法），而且可与其它对象比较（依据`__eq__()`方法），那么这个对象就是可哈希的。
两个可哈希对象仅当哈希码相等时相等。

## 字典的默认值

```python
import sys
import re
import collections

WORD_RE = re.compile(r'\w+')

# 创建一个有默认值的字典，指定list作为默认值的default factory
index = collections.defaultdict(list)
# 读取参数传递的文本文件中的单词并统计出现位置
with open(sys.argv[1], 'r', encoding='UTF-8') as fp:
    for line_no, line in enumerate(fp, 1):
        for match in WORD_RE.finditer(line):
            word = match.group()
            column_no = match.start() + 1
            location = line_no, column_no
            # default factory 仅为 __getitem__ 提供默认值
            index[word].append(location)

for word in sorted(index.keys(), key=str.upper):
    print(word, index[word])
```

```python
import collections
from types import MappingProxyType


class StrKeyDict(collections.UserDict):
    def __missing__(self, key):
        # 检查类型，防止下面的调用进入递归
        # 因为找不到 key 时默认就会进入此特殊方法
        if isinstance(key, str):
            raise KeyError(key)
        return self[str(key)]

    def __contains__(self, key):
        # data 是 UserDict 内置的 dict，可以读写此数据使操作表现保持一致
        return str(key) in self.data

    def __setitem__(self, key, value):
        self.data[str(key)] = value


d = StrKeyDict([('2', 'two',), ('4', 'four',)])
print(d['2'])
print(d[2])
print(d.get(3, 'N/A'))
print(1 in d)
try:
    print(d[True])
except KeyError:
    print('--- KeyError')

# 代理只能访问, 不能对其进行更新操作
d_proxy = MappingProxyType(d)
print(d_proxy['2'])
d['3'] = 'three'
print(d_proxy)
```

## 字典推导

```python
dial_codes = [
    (880, 'Bangladesh'),
    (55, 'Brazil'),
    (86, 'China'),
    (91, 'India'),
    (62, 'Indonesia'),
    (81, 'Japan'),
    (234, 'Nigeria'),
    (92, 'Pakistan'),
    (7, 'Russia'),
    (1, 'United States'),
]

country_dial = {country: code for code, country in dial_codes}

print(country_dial)

country_dial = {code: country.upper()
                for country, code in sorted(country_dial.items())
                if code < 70}

print(country_dial)
```

## 模式匹配

```python
"""
Pattern matching with mapping—requires Python ≥ 3.10

# tag::DICT_MATCH_TEST[]
>>> b1 = dict(api=1, author='Douglas Hofstadter',
...         type='book', title='Gödel, Escher, Bach')
>>> get_creators(b1)
['Douglas Hofstadter']
>>> from collections import OrderedDict
>>> b2 = OrderedDict(api=2, type='book',
...         title='Python in a Nutshell',
...         authors='Martelli Ravenscroft Holden'.split())
>>> get_creators(b2)
['Martelli', 'Ravenscroft', 'Holden']
>>> get_creators({'type': 'book', 'pages': 770})
Traceback (most recent call last):
    ...
ValueError: Invalid 'book' record: {'type': 'book', 'pages': 770}
>>> get_creators('Spam, spam, spam')
Traceback (most recent call last):
    ...
ValueError: Invalid record: 'Spam, spam, spam'

# end::DICT_MATCH_TEST[]
"""

# tag::DICT_MATCH[]
def get_creators(record: dict) -> list:
    match record:
        case {'type': 'book', 'api': 2, 'authors': [*names]}:  # <1>
            return names
        case {'type': 'book', 'api': 1, 'author': name}:  # <2>
            return [name]
        case {'type': 'book'}:  # <3>
            raise ValueError(f"Invalid 'book' record: {record!r}")
        case {'type': 'movie', 'director': name}:  # <4>
            return [name]
        case _:  # <5>
            raise ValueError(f'Invalid record: {record!r}')
# end::DICT_MATCH[]
```

>不同于序列模式匹配，即使只有部分匹配成功，也不会出现错误

## 集合

```python
from unicodedata import name

a = set(range(10))
b = set(range(5, 15))

# 并集
print(a | b)
# 交集
print(a & b)
# 差集
print(a - b)
print(b - a)
# 对称差集(补集)
print(a ^ b)

s = {1, 2}
z = {1, 2, 3}

# 包含于
print(z in s)
# 子集
print(s <= z)
# 真子集
print(s < z)
# 真超集
print(s > z)
# 超集
print(z >= s)

latin_1 = {chr(i) for i in range(32, 256) if 'SIGN' in name(chr(i), '')}
print(latin_1)

print(latin_1.union(a, b))

# 不可变集合
print(frozenset(a ^ b))
```

Last Modified 2023-05-25
