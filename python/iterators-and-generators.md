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

Last Modified 2022-07-26
