# 对象引用

- 在 Python 中`==`是比较对象的值，而`is`则是比较两个对象的引用（通过`id()`方法获得）
- 默认情况下 Python 对数据做浅复制，存在有深层次的可变类型时只复制其引用

> 可以在`Python Tutor`上运行代码，查看内存是如何变化的

## 深浅复制

```python
"""
>>> import copy
>>> bus1 = Bus(['Alice', 'Bill', 'Claire', 'David'])
>>> bus2 = copy.copy(bus1)
>>> bus3 = copy.deepcopy(bus1)
>>> bus1.drop('Bill')
>>> bus2.passengers
['Alice', 'Claire', 'David']
>>> bus3.passengers
['Alice', 'Bill', 'Claire', 'David']
>>> bus1.passengers is bus2.passengers
True
"""

import copy


class Bus:
    def __init__(self, passengers=None):
        if passengers is None:
            self.passengers = []
        else:
            self.passengers = list(passengers)

    def pick(self, name):
        self.passengers.append(name)

    def drop(self, name):
        self.passengers.remove(name)

    def __str__(self):
        return str(self.passengers)
```

> 如果需要在类实例的方法或单一函数中传递可变类型，需要谨慎考虑传入的数据是否会做修改或者是否要独立保存数据

在 CPython 中，垃圾回收使用的主要算法是引用计数。实际上，每个对象都会统计有多少引用指向自己。当引用计数归零时，对象立即被销毁: CPython 在对象上调用`__del__`方法（如果定义了），然后释放分配给对象的内存。

CPython 2.0 增加了分代垃圾回收算法，用于检测引用循环中涉及的对象组（如果一组对象之间全是相互引用），那么即使再出色的引用方式也会导致组中的对象不可达。有些 Python 的实现，垃圾回收程序更复杂，不依赖引用计数，这意味着对象的引用计数为零时可能不会立即调用`__del__`方法。

## 弱引用

```python
"""
>>> import weakref
>>> stock = weakref.WeakValueDictionary()
>>> catalog = [Cheese('Red Leicester'), Cheese('Tilsit'),
...                 Cheese('Brie'), Cheese('Parmesan')]
...
>>> for cheese in catalog:
...     stock[cheese.kind] = cheese
...
>>> sorted(stock.keys())
['Brie', 'Parmesan', 'Red Leicester', 'Tilsit']

# 在循环中使用的变量仍然存在
>>> 'cheese' in locals()
True
>>> def bye():
...    print('yet! like what?')
...
>>> ender = weakref.finalize(cheese, bye)
>>> ender.alive
True

>>> del catalog
>>> sorted(stock.keys())
['Parmesan']
>>> del cheese
yet! like what?
>>> sorted(stock.keys())
[]
>>> ender.alive
False
"""


class Cheese:

    def __init__(self, kind):
        self.kind = kind

    def __repr__(self):
        return 'Cheese(%r)' % self.kind
```

> 基本的`list`和`dict`实例不能作为弱引用所指对象，但其子类可以。`set`可以作为所指对象，`int`和`tuple`不能作为所指对象，子类也不可以。

Last Modified 2023-05-28
