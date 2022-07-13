# 将函数视为对象

## 经典的累加计算

```python
from functools import reduce
from operator import add


print(reduce(add, range(101)))

print(sum(range(101)))

class Obj:
    pass


def func():
    pass


# 函数有而对象没有的属性
print(sorted(set(dir(func)) - set(dir(Obj()))))
```

## 可调用的类

```python
"""
>>> bingo = BingoCage(range(3))
>>> bingo.pick()
1
>>> bingo()
0
>>> callable(bingo)
True
"""

import random


class BingoCage:

    def __init__(self, items):
        self._items = list(items)  # <1>
        random.shuffle(self._items)  # <2>

    def pick(self):  # <3>
        try:
            return self._items.pop()
        except IndexError:
            raise LookupError('pick from empty BingoCage')  # <4>

    def __call__(self):  # <5>
        return self.pick()
```

## 生成 HTML 标签

```python
"""
>>> tag('br')  # <1>
'<br />'
>>> tag('p', 'hello')  # <2>
'<p>hello</p>'
>>> print(tag('p', 'hello', 'world'))
<p>hello</p>
<p>world</p>
>>> tag('p', 'hello', id=33)  # <3>
'<p id="33">hello</p>'
>>> print(tag('p', 'hello', 'world', cls='sidebar'))  # <4>
<p class="sidebar">hello</p>
<p class="sidebar">world</p>
>>> tag(content='testing', name="img")  # <5>
'<img content="testing" />'
>>> my_tag = {'name': 'img', 'title': 'Sunset Boulevard',
...           'src': 'sunset.jpg', 'cls': 'framed'}
>>> tag(**my_tag)  # <6>
'<img class="framed" src="sunset.jpg" title="Sunset Boulevard" />'
"""


def tag(name, *content, cls=None, **attrs):
    """Generate one or more HTML tags"""
    if cls is not None:
        attrs['class'] = cls
    if attrs:
        attr_str = ''.join(' %s="%s"' % (attr, value)
                           for attr, value
                           in sorted(attrs.items()))
    else:
        attr_str = ''
    if content:
        return '\n'.join('<%s%s>%s</%s>' %
                         (name, attr_str, c, name) for c in content)
    else:
        return '<%s%s />' % (name, attr_str)
```

## 函数注解和解析

```python
from inspect import signature

"""
    >>> clip('banana ', 6)
    'banana'
    >>> clip('banana ', 5)
    'banana'
    >>> clip('banana split', 7)
    'banana'
    >>> clip('banana split', 11)
    'banana'
    >>> clip('banana split', 12)
    'banana split'
"""


def clip(text: str, max_len: 'int > 0' = 80) -> str:  # <1>
    """Return text clipped at the last space before or after max_len
    """
    end = None
    if len(text) > max_len:
        space_before = text.rfind(' ', 0, max_len)
        if space_before >= 0:
            end = space_before
        else:
            space_after = text.rfind(' ', max_len)
            if space_after >= 0:
                end = space_after
    if end is None:  # no spaces were found
        end = len(text)
    return text[:end].rstrip()


print(clip.__defaults__)
# (80,)
print(clip.__code__)  # doctest: +ELLIPSIS
# <code object clip at 0x...>
print(clip.__code__.co_varnames)
# ('text', 'max_len', 'end', 'space_before', 'space_after')
print(clip.__code__.co_argcount)
# 2


sig = signature(clip)
print(sig.return_annotation)
# <class 'str'>
for param in sig.parameters.values():
    note = repr(param.annotation).ljust(13)
    print(note, ':', param.name, '=', param.default)
# <class 'str'> : text = <class 'inspect._empty'>
# 'int > 0'     : max_len = 80
```

## operator 模块

```python
import operator
from operator import itemgetter
from operator import attrgetter
from operator import mul
from operator import methodcaller
from functools import reduce
from collections import namedtuple


def factorial(n):
    return reduce(mul, range(1, n + 1))


print(factorial(10))

metro_data = [
    ('Tokyo', 'JP', 36.933, (35.689722, 139.691667)),
    ('Delhi NCR', 'IN', 21.935, (28.613889, 77.208889)),
    ('Mexico City', 'MX', 20.142, (19.433333, -99.133333)),
    ('New York-Newark', 'US', 20.104, (40.808611, -74.020386)),
    ('Sao Paulo', 'BR', 19.649, (-23.547778, -46.635833)),
]

for city in sorted(metro_data, key=itemgetter(1)):
    print(city)

cc_name = itemgetter(1, 0)
for city in metro_data:
    print(cc_name(city))

LatLong = namedtuple('LatLong', 'lat long')
Metropolis = namedtuple('Metropolis', 'name cc pop coord')
metro_areas = [Metropolis(name, cc, pop, LatLong(lat, long))
               for name, cc, pop, (lat, long) in metro_data]
print(metro_areas[0])
print(metro_areas[0].coord.lat)
name_lat = attrgetter('name', 'coord.lat')
for city in sorted(metro_areas, key=attrgetter('coord.lat')):
    print(name_lat(city))

print([name for name in dir(operator) if not name.startswith('_')])

sentence = 'The time has come'
up_case = methodcaller('upper')
print(up_case(sentence))
hyphenate = methodcaller('replace', ' ', '-')
print(hyphenate(sentence))
```

## 冻结函数参数

```python
from operator import mul
from functools import partial
from unicodedata import normalize


def tag(name, *content, cls=None, **attrs):
    """Generate one or more HTML tags"""
    if cls is not None:
        attrs['class'] = cls
    if attrs:
        attr_str = ''.join(' %s="%s"' % (attr, value)
                           for attr, value
                           in sorted(attrs.items()))
    else:
        attr_str = ''
    if content:
        return '\n'.join('<%s%s>%s</%s>' %
                         (name, attr_str, c, name) for c in content)
    else:
        return '<%s%s />' % (name, attr_str)


triple = partial(mul, 3)
print([triple(k) for k in range(1, 10)])

nfc = partial(normalize, 'NFC')

s1 = 'café'
s2 = 'cafe\u0301'

print(nfc(s1) == nfc(s2))

picture = partial(tag, 'img', cls='pic-frame')
print(picture(src='boom.jpg'))
print(picture)
print(tag)
print(picture.func)
print(picture.args)
print(picture.keywords)
```

Last Modified 2022-07-13
