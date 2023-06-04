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

- 把接口继承和实现继承区分开
- 使用抽象基类显示表示接口
- 通过混入重用代码
- 在名称中明确指明混入
- 抽象基类可以作为混入，反过来则不成立
- 不要子类化多个具体类
- 为用户提供聚合类
- 优先使用对象组合，而不是类继承

Last Modified 2023-06-04
