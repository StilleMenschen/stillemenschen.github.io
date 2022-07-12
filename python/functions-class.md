# 将函数视为对象

## 经典的累加计算

```python
from functools import reduce
from operator import add


print(reduce(add, range(101)))

print(sum(range(101)))
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

Last Modified 2022-07-12
