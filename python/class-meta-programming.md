# 类元编程

## 简单的类工厂

```python
"""
factories.py

record_factory: create simple classes just for holding data fields

    >>> Dog = record_factory('Dog', 'name weight owner')  # <1>
    >>> rex = Dog('Rex', 30, 'Bob')
    >>> rex  # <2>
    Dog(name='Rex', weight=30, owner='Bob')
    >>> name, weight, _ = rex  # <3>
    >>> name, weight
    ('Rex', 30)
    >>> "{2}'s dog weighs {1}kg".format(*rex)  # <4>
    "Bob's dog weighs 30kg"
    >>> rex.weight = 32  # <5>
    >>> rex
    Dog(name='Rex', weight=32, owner='Bob')
    >>> Dog.__mro__  # <6>
    (<class 'factories.Dog'>, <class 'object'>)


The factory also accepts a list or tuple of identifiers:

    >>> Dog = record_factory('Dog', ['name', 'weight', 'owner'])
    >>> Dog.__slots__
    ('name', 'weight', 'owner')

"""


def record_factory(cls_name, field_names):
    try:
        field_names = field_names.replace(',', ' ').split()  # <1>
    except AttributeError:  # no .replace or .split
        pass  # assume it's already a sequence of identifiers
    field_names = tuple(field_names)  # <2>

    def __init__(self, *args, **kwargs):  # <3>
        attrs = dict(zip(self.__slots__, args))
        attrs.update(kwargs)
        for name, value in attrs.items():
            setattr(self, name, value)

    def __iter__(self):  # <4>
        for name in self.__slots__:
            yield getattr(self, name)

    def __repr__(self):  # <5>
        values = ', '.join('{}={!r}'.format(*i) for i
                           in zip(self.__slots__, self))
        return '{}({})'.format(self.__class__.__name__, values)

    cls_attrs = dict(__slots__=field_names,  # <6>
                     __init__=__init__,
                     __iter__=__iter__,
                     __repr__=__repr__)

    return type(cls_name, (object,), cls_attrs)  # <7>
```

>通过工厂函数创建的类不能序列化，即不能通过`pickle`模块里的`dump/load`函数处理

## 类的加载顺序

evalsupport.py

```python
print('<[100]> evalsupport module start')


def deco_alpha(cls):
    print('<[200]> deco_alpha')

    def inner_1(self):
        print('<[300]> deco_alpha:inner_1')

    cls.method_y = inner_1
    return cls


class MetaAleph(type):
    print('<[400]> MetaAleph body')

    def __init__(cls, name, bases, dic):
        print('<[500]> MetaAleph.__init__')

        def inner_2(self):
            print('<[600]> MetaAleph.__init__:inner_2')

        cls.method_z = inner_2


print('<[700]> evalsupport module end')
```

evaltime.py

```python
from evalsupport import deco_alpha

print('<[1]> evaltime module start')


class ClassOne:
    print('<[2]> ClassOne body')

    def __init__(self):
        print('<[3]> ClassOne.__init__')

    def __del__(self):
        print('<[4]> ClassOne.__del__')

    def method_x(self):
        print('<[5]> ClassOne.method_x')

    class ClassTwo(object):
        print('<[6]> ClassTwo body')


@deco_alpha
class ClassThree:
    print('<[7]> ClassThree body')

    def method_y(self):
        print('<[8]> ClassThree.method_y')


class ClassFour(ClassThree):
    print('<[9]> ClassFour body')

    def method_y(self):
        print('<[10]> ClassFour.method_y')


if __name__ == '__main__':
    print('<[11]> ClassOne tests', 30 * '.')
    one = ClassOne()
    one.method_x()
    print('<[12]> ClassThree tests', 30 * '.')
    three = ClassThree()
    three.method_y()
    print('<[13]> ClassFour tests', 30 * '.')
    four = ClassFour()
    four.method_y()

print('<[14]> evaltime module end')
```

进入命令行执行导入`evaltime.py`和直接执行`python3 evaltime.py`将会有不同的加载顺序

```
>>> import evaltime
<[100]> evalsupport module start
<[400]> MetaAleph body
<[700]> evalsupport module end
<[1]> evaltime_meta module start
<[2]> ClassThree body
<[200]> deco_alpha
<[4]> ClassFour body
<[6]> ClassFive body
<[500]> MetaAleph.__init__
<[9]> ClassSix body
<[500]> MetaAleph.__init__
<[15]> evaltime_meta module end
```

```
> python3 evaltime.py
<[100]> evalsupport module start
<[400]> MetaAleph body
<[700]> evalsupport module end
<[1]> evaltime_meta module start
<[2]> ClassThree body
<[200]> deco_alpha
<[4]> ClassFour body
<[6]> ClassFive body
<[500]> MetaAleph.__init__
<[9]> ClassSix body
<[500]> MetaAleph.__init__
<[11]> ClassThree tests ..............................
<[300]> deco_alpha:inner_1
<[12]> ClassFour tests ..............................
<[5]> ClassFour.method_y
<[13]> ClassFive tests ..............................
<[7]> ClassFive.__init__
<[600]> MetaAleph.__init__:inner_2
<[14]> ClassSix tests ..............................
<[7]> ClassFive.__init__
<[600]> MetaAleph.__init__:inner_2
<[15]> evaltime_meta module end
```

Last Modified 2022-08-10
