# 迭代器与生成器

## 迭代器

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

Last Modified 2022-07-30
