# Abstract Base Class

## 抽象基类

从 Python 2.6 开始，标准库提供了多个抽象基类，大都在 collections.abc 模块中定义，不过其它地方也有，例如，io 包和 numbers 包中就有一些抽家基类。但是，collections.abc 中的抽象基类最常用。

```python
import abc

class Tombola(abc.ABC):  # <1>

    @abc.abstractmethod
    def load(self, iterable):  # <2>
        """Add items from an iterable."""

    @abc.abstractmethod
    def pick(self):  # <3>
        """Remove item at random, returning it.

        This method should raise `LookupError` when the instance is empty.
        """

    def loaded(self):  # <4>
        """Return `True` if there's at least 1 item, `False` otherwise."""
        return bool(self.inspect())  # <5>


    def inspect(self):
        """Return a sorted tuple with the items currently inside."""
        items = []
        while True:  # <6>
            try:
                items.append(self.pick())
            except LookupError:
                break
        self.load(items)  # <7>
        return tuple(sorted(items))
```

> LookupError 是 KeyError、IndexError 的基类，参考 https://docs.python.org/zh-cn/3.11/library/exceptions.html#exception-hierarchy

```python
"""
Variation of ``tombola.Tombola`` implementing ``__subclasshook__``.

Tests with simple classes::

    >>> Tombola.__subclasshook__(object)
    NotImplemented
    >>> class Complete:
    ...     def __init__(): pass
    ...     def load(): pass
    ...     def pick(): pass
    ...     def loaded(): pass
    ...
    >>> Tombola.__subclasshook__(Complete)
    True
    >>> issubclass(Complete, Tombola)
    True

"""


from abc import ABC, abstractmethod
from inspect import getmembers, isfunction


class Tombola(ABC):  # <1>

    @abstractmethod
    def __init__(self, iterable):  # <2>
        """New instance is loaded from an iterable."""

    @abstractmethod
    def load(self, iterable):
        """Add items from an iterable."""

    @abstractmethod
    def pick(self):  # <3>
        """Remove item at random, returning it.

        This method should raise `LookupError` when the instance is empty.
        """

    def loaded(self):  # <4>
        try:
            item = self.pick()
        except LookupError:
            return False
        else:
            self.load([item])  # put it back
            return True

    @classmethod
    def __subclasshook__(cls, other_cls):
        if cls is Tombola:
            interface_names = function_names(cls)
            found_names = set()
            for a_cls in other_cls.__mro__:
                found_names |= function_names(a_cls)
            if found_names >= interface_names:
                return True
        return NotImplemented


def function_names(obj):
    return {name for name, _ in getmembers(obj, isfunction)}
```

> @abstractmethod 之上可以叠放其它装饰器，如 @classmethod 或者 @staticmethod ，但 @abstractmethod 和 def 之间不可以有其它装饰器

## 实现类

```python
import random

from tombola import Tombola


class BingoCage(Tombola):  # <1>

    def __init__(self, items):
        # 用从操作系统提供的源生成随机数
        self._randomizer = random.SystemRandom()  # <2>
        self._items = []
        self.load(items)  # <3>

    def load(self, items):
        self._items.extend(items)
        self._randomizer.shuffle(self._items)  # <4>

    def pick(self):  # <5>
        try:
            return self._items.pop()
        except IndexError:
            raise LookupError('pick from empty BingoCage')

    def __call__(self):  # <7>
        self.pick()
```

```python
import random

from tombola import Tombola


class LotteryBlower(Tombola):

    def __init__(self, iterable):
        self._balls = list(iterable)  # <1>

    def load(self, iterable):
        self._balls.extend(iterable)

    def pick(self):
        try:
            position = random.randrange(len(self._balls))  # <2>
        except ValueError:
            raise LookupError('pick from empty LotteryBlower')
        return self._balls.pop(position)  # <3>

    def loaded(self):  # <4>
        return bool(self._balls)

    def inspect(self):  # <5>
        return tuple(sorted(self._balls))
```

```python
from random import randrange

from tombola import Tombola


@Tombola.register  # <1>
class TomboList(list):  # <2>

    def pick(self):
        if self:  # <3>
            position = randrange(len(self))
            return self.pop(position)  # <4>
        else:
            raise LookupError('pop from empty TomboList')

    load = list.extend  # <5>

    def loaded(self):
        return bool(self)  # <6>

    def inspect(self):
        return tuple(sorted(self))

# Tombola.register(TomboList)  # <7>
```

```python
from random import shuffle

from tombola import Tombola


class TumblingDrum(Tombola):

    def __init__(self, iterable):
        self._balls = []
        self.load(iterable)

    def load(self, iterable):
        self._balls.extend(iterable)
        shuffle(self._balls)

    def pick(self):
        return self._balls.pop()
```

## 验证子类

```rst
==============
Tombola tests
==============

Every concrete subclass of Tombola should pass these tests.


Create and load instance from iterable::

    >>> balls = list(range(3))
    >>> globe = ConcreteTombola(balls)
    >>> globe.loaded()
    True
    >>> globe.inspect()
    (0, 1, 2)


Pick and collect balls::

    >>> picks = []
    >>> picks.append(globe.pick())
    >>> picks.append(globe.pick())
    >>> picks.append(globe.pick())


Check state and results::

    >>> globe.loaded()
    False
    >>> sorted(picks) == balls
    True


Reload::

    >>> globe.load(balls)
    >>> globe.loaded()
    True
    >>> picks = [globe.pick() for i in balls]
    >>> globe.loaded()
    False


Check that `LookupError` (or a subclass) is the exception
thrown when the device is empty::

    >>> globe = ConcreteTombola([])
    >>> try:
    ...     globe.pick()
    ... except LookupError as exc:
    ...     print('OK')
    OK


Load and pick 100 balls to verify that they all come out::

    >>> balls = list(range(100))
    >>> globe = ConcreteTombola(balls)
    >>> picks = []
    >>> while globe.inspect():
    ...     picks.append(globe.pick())
    >>> len(picks) == len(balls)
    True
    >>> set(picks) == set(balls)
    True


Check that the order has changed and is not simply reversed::

    >>> picks != balls
    True
    >>> picks[::-1] != balls
    True

Note: the previous 2 tests have a *very* small chance of failing
even if the implementation is OK. The probability of the 100
balls coming out, by chance, in the order they were inspect is
1/100!, or approximately 1.07e-158. It's much easier to win the
Lotto or to become a billionaire working as a programmer.

THE END
```

```python
import doctest

from tombola import Tombola

# modules to test
import bingo, lotto, tombolist, drum  # <1>

TEST_FILE = 'tombola_tests.rst'
TEST_MSG = '{0:16} {1.attempted:2} tests, {1.failed:2} failed - {2}'


def main(argv):
    verbose = '-v' in argv
    real_subclasses = Tombola.__subclasses__()  # <2>
    # 在 python 3.10 中已经不允许直接获取抽象基类, 只能通过 abc 模块中的 get_cache_token() 方法做相等性测试

    for cls in real_subclasses:  # <4>
        test(cls, verbose)


def test(cls, verbose=False):

    res = doctest.testfile(
            TEST_FILE,
            globs={'ConcreteTombola': cls},  # <5>
            verbose=verbose,
            optionflags=doctest.REPORT_ONLY_FIRST_FAILURE)
    tag = 'FAIL' if res.failed else 'OK'
    print(TEST_MSG.format(cls.__name__, res, tag))  # <6>


if __name__ == '__main__':
    import sys
    main(sys.argv)
```

> 关于虚拟子类的比较参考：https://docs.python.org/zh-cn/3/library/abc.html#abc.get_cache_token

> 关于数值的抽象基类`numbers`可参考：https://docs.python.org/zh-cn/3/library/numbers.html

Last Modified 2022-07-24
