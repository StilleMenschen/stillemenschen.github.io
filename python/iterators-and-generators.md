# 迭代器与生成器

## 可迭代对象

使用内置函数 iter 可以获取迭代器的对象。如果对象实现了能返回迭代器的`__iter__`方法，那么对象就是可迭代的。序列都可以迭代。实现了`__getitem__`方法，而且接受从 0 开始的索引，这种对象也可以迭代。

```python
"""
Sentence: access words by index

    >>> text = 'To be, or not to be, that is the question'
    >>> s = Sentence(text)
    >>> len(s)
    10
    >>> s[1], s[5]
    ('be', 'be')
    >>> s
    Sentence('To be, or no... the question')

"""

import re
import reprlib

RE_WORD = re.compile(r'\w+')


class Sentence:

    def __init__(self, text):
        self.text = text
        self.words = RE_WORD.findall(text)  # <1>

    def __getitem__(self, index):
        return self.words[index]  # <2>

    def __len__(self):  # <3>
        return len(self.words)

    def __repr__(self):
        return 'Sentence(%s)' % reprlib.repr(self.text)  # <4>
```

## 迭代器

迭代器耗尽后不可以重置，必须重新创建迭代器，调用`iter()`

```python
"""
Sentence: iterate over words using the Iterator Pattern, take #2

WARNING: the Iterator Pattern is much simpler in idiomatic Python;
see: sentence_gen*.py.
"""

import re
import reprlib

RE_WORD = re.compile(r'\w+')


class Sentence:

    def __init__(self, text):
        self.text = text

    def __repr__(self):
        return 'Sentence(%s)' % reprlib.repr(self.text)

    def __iter__(self):
        word_iter = RE_WORD.finditer(self.text)  # <1>
        return SentenceIter(word_iter)  # <2>


class SentenceIter():

    def __init__(self, word_iter):
        self.word_iter = word_iter  # <3>

    def __next__(self):
        match = next(self.word_iter)  # <4>
        return match.group()  # <5>

    def __iter__(self):
        return self
```

> 如果要自行实现`__next__`方法，在迭代耗尽时应当抛出`StopIteration`，详见`collections.abc.Iterator`

## 生成器

```python
"""
Sentence: iterate over words using a generator function

>>> s = Sentence('The time has come')
>>> s
Sentence('The time has come')
>>> list(s)
['The', 'time', 'has', 'come']
>>> it = iter(s)
>>> next(it)
'The'
>>> next(it)
'time'
>>> next(it)
'has'
>>> next(it)
'come'
>>> next(it)
Traceback (most recent call last):
  ...
StopIteration

>>> s = Sentence('"The time has come," the Walrus said,')
>>> s
Sentence('"The time ha... Walrus said,')
>>> list(s)
['The', 'time', 'has', 'come', 'the', 'Walrus', 'said']
>>> s = Sentence('''"The time has come," the Walrus said,
...                 "To talk of many things:"''')
>>> s
Sentence('"The time ha...many things:"')
>>> list(s)
['The', 'time', 'has', 'come', 'the', 'Walrus', 'said', 'To', 'talk', 'of', 'many', 'things']
>>> s = Sentence('Agora vou-me. Ou me vão?')
>>> s
Sentence('Agora vou-me. Ou me vão?')
>>> list(s)
['Agora', 'vou', 'me', 'Ou', 'me', 'vão']
"""

import re
import reprlib

RE_WORD = re.compile(r'\w+')


class Sentence:

    def __init__(self, text):
        self.text = text  # <1>

    def __repr__(self):
        return 'Sentence(%s)' % reprlib.repr(self.text)

    def __iter__(self):
        return (match.group() for match in RE_WORD.finditer(self.text))  # <2>
```

## 等差数列

```python
"""
ArithmeticProgression Tests:

    >>> ap = ArithmeticProgression(1, .5, 5.5)
    >>> list(ap)
    [1.0, 1.5, 2.0, 2.5, 3.0, 3.5, 4.0, 4.5, 5.0]
    >>> ap = ArithmeticProgression(0, 2)
    >>> for value in ap:
    ...     if value > 10:
    ...         break
    ...     print(value)
    0
    2
    4
    6
    8
    10
    >>> apg = aritprog_gen(1, .5, 5.5)
    >>> list(apg)
    [1.0, 1.5, 2.0, 2.5, 3.0, 3.5, 4.0, 4.5, 5.0]
"""
import itertools


class ArithmeticProgression:
    def __init__(self, begin, step, end=None):
        self.begin = begin
        self.step = step
        self.end = end

    def __iter__(self):
        result = type(self.begin + self.step)(self.begin)
        forever = self.end is None
        index = 0
        while forever or result < self.end:
            yield result
            index += 1
            result = self.begin + self.step * index


# 因为允许传入 int 或 float 类型，如果传入的是 int 和 float 则取 float 类型生成
def aritprog_gen(begin, step, end=None):
    first = type(begin + step)(begin)
    ap_gen = itertools.count(first, step)
    if end is not None:
        ap_gen = itertools.takewhile(lambda n: n < end, ap_gen)
    return ap_gen
```

## itertools 模块

```python
"""
itertools Tests:

    >>> import itertools
    >>> gen = itertools.count(1, .5)
    >>> next(gen)
    1
    >>> next(gen)
    1.5
    >>> next(gen)
    2.0
    >>> next(gen)
    2.5
    >>> gen = itertools.takewhile(lambda n: n < 10, itertools.count(0, 2))
    >>> list(gen)
    [0, 2, 4, 6, 8]
    >>> list(itertools.compress('Aardvark', (1,0,1,0,1,1,0)))
    ['A', 'r', 'v', 'a']
    >>> list(itertools.islice('Aardvark', 1, 7, 2))
    ['a', 'd', 'a']
    >>> list(itertools.filterfalse(lambda x: x & 1 == 1, range(20)))
    [0, 2, 4, 6, 8, 10, 12, 14, 16, 18]
    >>> list(itertools.dropwhile(lambda c: c.lower() in 'abc', 'aBCdefgabc'))
    ['d', 'e', 'f', 'g', 'a', 'b', 'c']
    >>> sample = [5, 4, 2, 9, 1, 3, 0, 1, 7]
    >>> list(itertools.accumulate(sample))
    [5, 9, 11, 20, 21, 24, 24, 25, 32]
    >>> list(itertools.accumulate(sample, min))
    [5, 4, 2, 2, 1, 1, 0, 0, 0]
    >>> list(itertools.accumulate(sample, max))
    [5, 5, 5, 9, 9, 9, 9, 9, 9]
    >>> import operator
    >>> list(itertools.accumulate(range(1, 8), operator.mul))
    [1, 2, 6, 24, 120, 720, 5040]
    >>> list(enumerate('ABCDEFG', 1))
    [(1, 'A'), (2, 'B'), (3, 'C'), (4, 'D'), (5, 'E'), (6, 'F'), (7, 'G')]
    >>> list(map(operator.mul, range(1, 11), [2, 4, 8]))
    [2, 8, 24]
    >>> list(itertools.starmap(operator.mul, enumerate('aeiou', 1)))
    ['a', 'ee', 'iii', 'oooo', 'uuuuu']
    >>> list(itertools.starmap(lambda a, b: b / a, enumerate(itertools.accumulate(range(2, 30, 2)), 1)))
    [2.0, 3.0, 4.0, 5.0, 6.0, 7.0, 8.0, 9.0, 10.0, 11.0, 12.0, 13.0, 14.0, 15.0]
    >>> list(itertools.chain(enumerate('ABC')))
    [(0, 'A'), (1, 'B'), (2, 'C')]
    >>> list(itertools.chain.from_iterable(enumerate('ABC')))
    [0, 'A', 1, 'B', 2, 'C']
    >>> list(zip('ABC', range(5)))
    [('A', 0), ('B', 1), ('C', 2)]
    >>> list(zip('ABC', range(5), [10, 20, 30, 50]))
    [('A', 0, 10), ('B', 1, 20), ('C', 2, 30)]
    >>> list(itertools.zip_longest('ABC', range(5), [10, 20, 30, 50], fillvalue='?'))
    [('A', 0, 10), ('B', 1, 20), ('C', 2, 30), ('?', 3, 50), ('?', 4, '?')]
    >>> suits = 'spades diamonds clubs heart'.split()
    >>> list(itertools.product('AK', suits))
    [('A', 'spades'), ('A', 'diamonds'), ('A', 'clubs'), ('A', 'heart'), ('K', 'spades'), ('K', 'diamonds'), ('K', 'clubs'), ('K', 'heart')]
    >>> list(itertools.product(range(2), repeat=3))
    [(0, 0, 0), (0, 0, 1), (0, 1, 0), (0, 1, 1), (1, 0, 0), (1, 0, 1), (1, 1, 0), (1, 1, 1)]
    >>> list(itertools.islice(itertools.count(1, .3), 7))
    [1, 1.3, 1.6, 1.9000000000000001, 2.2, 2.5, 2.8]
    >>> list(map(operator.mul, itertools.cycle([1, 3, 7]), itertools.repeat(5, 10)))
    [5, 15, 35, 5, 15, 35, 5, 15, 35, 5]
    >>> list(itertools.combinations('ABC', 2))
    [('A', 'B'), ('A', 'C'), ('B', 'C')]
    >>> list(itertools.combinations_with_replacement('ABC', 2))
    [('A', 'A'), ('A', 'B'), ('A', 'C'), ('B', 'B'), ('B', 'C'), ('C', 'C')]
    >>> list(itertools.permutations('ABC', 2))
    [('A', 'B'), ('A', 'C'), ('B', 'A'), ('B', 'C'), ('C', 'A'), ('C', 'B')]

    >>> for char, group in itertools.groupby('LLLLAAAGGG'):
    ...    print(char, '->', list(group))
    ...
    L -> ['L', 'L', 'L', 'L']
    A -> ['A', 'A', 'A']
    G -> ['G', 'G', 'G']
    >>> animals = ['duck', 'eagle', 'rat', 'giraffe', 'bear', 'bat', 'dolphin', 'shark', 'lion']
    >>> animals.sort(key=len)
    >>> for length, group in itertools.groupby(animals, len):
    ...    print(length, '->', list(group))
    ...
    3 -> ['rat', 'bat']
    4 -> ['duck', 'bear', 'lion']
    5 -> ['eagle', 'shark']
    7 -> ['giraffe', 'dolphin']
    >>> for length, group in itertools.groupby(reversed(animals), len):
    ...    print(length, '->', list(group))
    ...
    7 -> ['dolphin', 'giraffe']
    5 -> ['shark', 'eagle']
    4 -> ['lion', 'bear', 'duck']
    3 -> ['bat', 'rat']
    >>> g1, g2 = itertools.tee('ABC')
    >>> next(g1), next(g1), next(g2)
    ('A', 'B', 'A')
    >>> list(zip(*itertools.tee('ABC', 3)))
    [('A', 'A', 'A'), ('B', 'B', 'B'), ('C', 'C', 'C')]
"""
```

```python
"""
>>> s = 'ABC'
>>> t = tuple(range(1, 4))
>>> list(chain(s, t))
['A', 'B', 'C', 1, 2, 3]
>>> list(chain_form(s, t))
['A', 'B', 'C', 1, 2, 3]
"""


def chain(*iterables):
    for it in iterables:
        for i in it:
            yield i


def chain_form(*iterables):
    for it in iterables:
        yield from it
```

## iter 函数

```python
import random


def d6():
    return random.randint(1, 6)


# 连续掷骰子一直到获得点数 3
d6_iter_one = iter(d6, 3)
for d in d6_iter_one:
    print(d)

text = '''Python Assignment Operators

Assignment operators include the basic assignment operator equal to sign (=).
'''

with open('dummy', 'w', encoding='utf8') as f:
    f.write(text)

with open('dummy', 'r', encoding='utf8') as fp:
    # 遇到行结束符停止
    for line in iter(fp.readline, '\n'):
        print(line, end='')
```

## yield 关键字

```python
def tree(cls, level=0):
    yield cls.__name__, level
    # 终止递归的条件是 __subclasses__ 没有元素
    for sub_cls in cls.__subclasses__():
        yield from tree(sub_cls, level + 1)


def display(cls):
    for cls_name, level in tree(cls):
        indent = ' ' * 4 * level
        print(f'{indent}{cls_name}')


if __name__ == '__main__':
    display(BaseException)
```

```python
"""
A coroutine to compute a running average

    >>> coro_avg = averager()  # <1>
    >>> next(coro_avg)  # <2>
    0.0
    >>> coro_avg.send(10)  # <3>
    10.0
    >>> coro_avg.send(30)
    20.0
    >>> coro_avg.send(5)
    15.0


    >>> coro_avg.send(20)  # <1>
    16.25
    >>> coro_avg.close()  # <2>
    >>> coro_avg.close()  # <3>
    >>> coro_avg.send(5)  # <4>
    Traceback (most recent call last):
      ...
    StopIteration


"""

from collections.abc import Generator


def averager() -> Generator[float, float, None]:  # <1>
    total = 0.0
    count = 0
    average = 0.0
    while True:  # <2>
        term = yield average  # <3>
        total += term
        count += 1
        average = total / count
```

```python
"""
A coroutine to compute a running average.

Testing ``averager2`` by itself::


    >>> coro_avg = averager2()
    >>> next(coro_avg)
    >>> coro_avg.send(10)  # <1>
    >>> coro_avg.send(30)
    >>> coro_avg.send(6.5)
    >>> coro_avg.close()  # <2>


Catching `StopIteration` to extract the value returned by
the coroutine::


    >>> coro_avg = averager2()
    >>> next(coro_avg)
    >>> coro_avg.send(10)
    >>> coro_avg.send(30)
    >>> coro_avg.send(6.5)
    >>> try:
    ...     coro_avg.send(STOP)  # <1>
    ... except StopIteration as exc:
    ...     result = exc.value  # <2>
    ...
    >>> result  # <3>
    Result(count=3, average=15.5)


Using `yield from`:



    >>> def compute():
    ...     res = yield from averager2(True)  # <1>
    ...     print('computed:', res)  # <2>
    ...     return res  # <3>
    ...
    >>> comp = compute()  # <4>
    >>> for v in [None, 10, 20, 30, STOP]:  # <5>
    ...     try:
    ...         comp.send(v)  # <6>
    ...     except StopIteration as exc:  # <7>
    ...         result = exc.value
    received: 10
    received: 20
    received: 30
    received: <Sentinel>
    computed: Result(count=3, average=20.0)
    >>> result  # <8>
    Result(count=3, average=20.0)

"""

from collections.abc import Generator
from typing import Union, NamedTuple


class Result(NamedTuple):  # <1>
    count: int  # type: ignore  # <2>
    average: float


class Sentinel:  # <3>
    def __repr__(self):
        return f'<Sentinel>'


STOP = Sentinel()  # <4>

SendType = Union[float, Sentinel]  # <5>


def averager2(verbose: bool = False) -> Generator[None, SendType, Result]:  # <1>
    total = 0.0
    count = 0
    average = 0.0
    while True:
        term = yield  # <2>
        if verbose:
            print('received:', term)
        if isinstance(term, Sentinel):  # <3>
            break
        total += term  # <4>
        count += 1
        average = total / count
    return Result(count, average)  # <5>
```

Last Modified 2023-06-09
