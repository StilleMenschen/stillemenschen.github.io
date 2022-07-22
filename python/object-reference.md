# 对象引用

- 在 Python 中`==`是比较对象的值，而`is`则是比较两个对象的引用（通过`id()`方法获得）
- 默认情况下 Python 对数据做浅复制，存在有深层次的可变类型时只复制其引用

>可以在`Python Tutor`上运行代码，查看内存是如何变化的

## 浅复制和深复制

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

>如果需要在类实例的方法或单一函数中传递可变类型，需要谨慎考虑传入的数据是否会做修改或者是否要独立保存数据

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
>>> del catalog
>>> sorted(stock.keys())
['Parmesan']
>>> del cheese
>>> sorted(stock.keys())
[]
"""


class Cheese:

    def __init__(self, kind):
        self.kind = kind

    def __repr__(self):
        return 'Cheese(%r)' % self.kind
```

>基本的`list`和`dict`实例不能作为弱引用所指对象，但其子类可以。
> `set`可以作为所指对象，`int`和`tuple`不能作为所指对象，子类也不可以。

Last Modified 2022-07-21
