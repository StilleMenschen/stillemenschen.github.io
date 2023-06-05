# 类继承

## 调用顺序

方法解析顺序只决定唤醒顺序，至于各个类中相应的方法是否被唤醒，则取决于实现方法时有没有调用`super()`。

以下面实验中的`pong`方法为例。由于 Leaf 类没有覆盖该方法，因此调用`leaf1.pong()`唤醒的是`Leaf.__mro__`中下一个类(A 类)实现的`pong`方法。`A.pong`方法调用了`super().pong()`。方法解析顺序的下一个类是 B，因此`B.pong`被唤醒。但是，因为`B.pong`方法没有调用`super().pong()`，所以唤醒过程到此结束。

方法解析顺序不仅考虑继承图，还考虑子类声明罗列超类的顺序。也就是说，在 diamond.py 文件中，如果把 Leaf 类声明为`Leaf(B， A)`，那么在`Leaf.__mro__`中，B 类将出现在 A 类前面。这会影响`ping`方法的唤醒顺序，而且`leaf1.pong()`将通过继承树唤醒`B.pong`，但是不唤醒`A.pong`和`Root.pong`，因为`B.pong`没有调用`super()`。

调用`super()`的方法叫协作方法（cooperative method）。利用协作方法可以实现协作多重继承。这两个术语就是字面意思，Python 中的多重继承涉及多个方法的协作。在 B 类中，`ping`是协作方法，而`pong`则不是。

```python
"""
diamond1.py: Demo of diamond-shaped class graph.

>>> Leaf.__mro__  # doctest: +NORMALIZE_WHITESPACE
    (<class 'diamond1.Leaf'>, <class 'diamond1.A'>, <class 'diamond1.B'>,
    <class 'diamond1.Root'>, <class 'object'>)


    >>> leaf1 = Leaf()  # <1>
    >>> leaf1.ping()    # <2>
    <instance of Leaf>.ping() in Leaf
    <instance of Leaf>.ping() in A
    <instance of Leaf>.ping() in B
    <instance of Leaf>.ping() in Root

    >>> leaf1.pong()   # <3>
    <instance of Leaf>.pong() in A
    <instance of Leaf>.pong() in B

"""


class Root:  # <1>
    def ping(self):
        print(f'{self}.ping() in Root')

    def pong(self):
        print(f'{self}.pong() in Root')

    def __repr__(self):
        cls_name = type(self).__name__
        return f'<instance of {cls_name}>'


class A(Root):  # <2>
    def ping(self):
        print(f'{self}.ping() in A')
        super().ping()

    def pong(self):
        print(f'{self}.pong() in A')
        super().pong()


class B(Root):  # <3>
    def ping(self):
        print(f'{self}.ping() in B')
        super().ping()

    def pong(self):
        print(f'{self}.pong() in B')


class Leaf(A, B):  # <4>
    def ping(self):
        print(f'{self}.ping() in Leaf')
        super().ping()
```

## 顺序继承

内置类型的原生方法使用 C 语言实现，不会调用子类中覆盖的方法

```python
import numbers


class A:
    def ping(self):
        print('A ping:', self)


class B(A):
    def pong(self):
        print('B pong:', self)


class C(A):
    def pong(self):
        print('C PONG:', self)


class D(B, C):

    def ping(self):
        super().ping()
        print('D post-ping:', self)

    def pingpong(self):
        self.ping()
        super().ping()
        self.pong()
        super().pong()
        C.pong(self)


def print_mro(cls):
    print(',  '.join(c.__name__ for c in cls.__mro__))


d = D()
d.ping()
print('-' * 20)
d.pong()
print('-' * 20)
d.pingpong()
print('-' * 20)
print_mro(D)
print_mro(numbers.Integral)
print_mro(bool)
```

## 混入类

```python
"""
Short demos
===========

``UpperDict`` behaves like a case-insensitive mapping`::

    >>> d = UpperDict([('a', 'letter A'), (2, 'digit two')])
    >>> list(d.keys())
    ['A', 2]
    >>> d['b'] = 'letter B'
    >>> 'b' in d
    True
    >>> d['a'], d.get('B')
    ('letter A', 'letter B')
    >>> list(d.keys())
    ['A', 2, 'B']


And ``UpperCounter`` is also case-insensitive::

    >>> c = UpperCounter('BaNanA')
    >>> c.most_common()
    [('A', 3), ('N', 2), ('B', 1)]


Detailed tests
==============

UpperDict uppercases all string keys.

    >>> d = UpperDict([('a', 'letter A'), ('B', 'letter B'), (2, 'digit two')])


Tests for item retrieval using `d[key]` notation::

    >>> d['A']
    'letter A'
    >>> d['b']
    'letter B'
    >>> d[2]
    'digit two'


Tests for missing key::

    >>> d['z']
    Traceback (most recent call last):
      ...
    KeyError: 'Z'
    >>> d[99]
    Traceback (most recent call last):
      ...
    KeyError: 99


Tests for item retrieval using `d.get(key)` notation::

    >>> d.get('a')
    'letter A'
    >>> d.get('B')
    'letter B'
    >>> d.get(2)
    'digit two'
    >>> d.get('z', '(not found)')
    '(not found)'

Tests for the `in` operator::

    >>> ('a' in d, 'B' in d, 'z' in d)
    (True, True, False)

Test for item assignment using lowercase key::

    >>> d['c'] = 'letter C'
    >>> d['C']
    'letter C'

Tests for update using a `dict` or a sequence of pairs::

    >>> d.update({'D': 'letter D', 'e': 'letter E'})
    >>> list(d.keys())
    ['A', 'B', 2, 'C', 'D', 'E']
    >>> d.update([('f', 'letter F'), ('G', 'letter G')])
    >>> list(d.keys())
    ['A', 'B', 2, 'C', 'D', 'E', 'F', 'G']
    >>> d  # doctest:+NORMALIZE_WHITESPACE
    {'A': 'letter A', 'B': 'letter B', 2: 'digit two',
    'C': 'letter C', 'D': 'letter D', 'E': 'letter E',
    'F': 'letter F', 'G': 'letter G'}

UpperCounter uppercases all `str` keys.

Test for initializer: keys are uppercased.

    >>> d = UpperCounter('AbracAdaBrA')
    >>> sorted(d.keys())
    ['A', 'B', 'C', 'D', 'R']

Tests for count retrieval using `d[key]` notation::

    >>> d['a']
    5
    >>> d['z']
    0

"""
import collections


def _upper(key):  # <1>
    try:
        return key.upper()
    except AttributeError:
        return key


class UpperCaseMixin:  # <2>
    def __setitem__(self, key, item):
        super().__setitem__(_upper(key), item)

    def __getitem__(self, key):
        return super().__getitem__(_upper(key))

    def get(self, key, default=None):
        return super().get(_upper(key), default)

    def __contains__(self, key):
        return super().__contains__(_upper(key))

# UpperCaseMixin 必须是第一个基类，否则会调用 UserDict 中的方法

class UpperDict(UpperCaseMixin, collections.UserDict):  # <1>
    pass


class UpperCounter(UpperCaseMixin, collections.Counter):  # <2>
    """Specialized 'Counter' that uppercases string keys"""  # <3>
```

## 特别注意

### 优先使用对象组合，而不是类继承

- 一旦适应了继承，很容易过度使用
- 利于组合的设计更灵活
- 即便是单一继承，也能提高灵活性，因为子类化是一种紧密的耦合，而且“参天大树”般的继承更容易被风刮倒

### 把接口继承和实现继承区分开

使用多重继承时，一定要明确一开始为什么创建子类。主要原因如下

- 继承接口，创建子类型，实现“是什么”关系。这种情况最好使用抽象基类。
- 继承实现，通过重用避免代码重复。这种情况可以利用混入。

其实这两条经常同时出现，不过只要可能，一定要明确意图。通过继承重用代码是实现细节，通常可以换用组合和委托。而接口继承则是框架的支柱。如果可能，接口继承只应使用抽象基类作为基类。

### 使用抽象基类显式表示接口

在现代的 Python 中，如果类的作用是定义接口，则应该显式地把它定义为抽象基类或者 typing.Protocol 的子类。抽象基类只能子类化 abc.ABC 或其他抽象基类。继承多个抽象基类不是问题。

### 通过混入重用代码

如果一个类的作用是提供方法实现以供多个不相关的子类重用，但不体现“是什么”关系，那么就应该把那个类明确地定义为混入类。从概念上讲，混入不定义新类型，只是打包方法，便于重用。混入类绝对不能实例化，而且具体类不能只继承混入类。混入类应该提供某方面的特定行为，只实现少量关系非常紧密的方法。混入类不应保持任何内部状态，即不应有实例属性。

Python 中没有表明一个类是混入类的正式方式，因此强烈建议在名称后面加上 Mixin。

### 为用户提供聚合类

如果一个类的结构主要继承自混入类，自身没有添加结构或行为，那么这样的类称为“聚合类”。

如果抽象基类或混入类的某种组合对客户代码非常有用，那么就提供一个类，以易于理解的方式把它们组合起来。

### 仅子类化为子类化设计的类

子类化复杂的类并覆盖类的方法容易出错，因为超类中的方法可能在不知不觉中忽略子类覆盖的行为。应尽量避免覆盖方法，至少要抑制冲动，再容易扩展的类也不轻易子类化。如果必须子类化，则还要看原类是不是为了扩展而设计的。

可以阅读文档查看类的说明，是否该类的行为应该由子类来覆盖实现

> 在 Python 3.8 以上的版本中，可以通过`@final`装饰器声明该类不应该子类化或者覆盖其方法，详见 PEP 591

### 不要子类化多个具体类

子类化具体类比子类化抽象基类和混入类还危险，因为具体类的实例通常有内部状态，覆盖依赖内部状态的方法时很容易破坏状态。即使是调用`super()`的协作方法，而且在使用`__x`句法的私有属性中保存内部状态，覆盖方法时也有无数种方式引入问题。

如果子类化是为了重用代码，那么想要重用的代码应该放入抽象基类或混入方法中，或者放入名称可以明确表明意图的混入类中。

> 参考的资料来自于《流畅的 Python：第二版》

Last Modified 2023-06-05
