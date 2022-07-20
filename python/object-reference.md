# 对象引用

- 在 Python 中`==`是比较对象的值，而`is`则是比较两个对象的引用（通过`id()`方法获得）
- 默认情况下 Python 对数据做浅复制，存在有深层次的可变类型时只复制其引用

> 可以在`Python Tutor`上运行代码，查看内存是如何变化的

## 浅复制和深复制

```python
import copy


class Bus:
    def __init__(self, passenger=None):
        if passenger is None:
            self.passenger = []
        else:
            self.passenger = list(passenger)

    def pick(self, name):
        self.passenger.append(name)

    def drop(self, name):
        self.passenger.remove(name)

    def __str__(self):
        return str(self.passenger)


bus1 = Bus(['Alice', 'Tom', 'Bob', 'David', 'Claire'])
bus2 = copy.copy(bus1)
bus3 = copy.deepcopy(bus1)

bus1.pick('John')
bus2.drop('Bob')
bus2.drop('Tom')
bus3.pick('Jack')

print(bus1)
print(bus2)
print(bus3)
print(id(bus1), id(bus2), id(bus3))
print(id(bus1.passenger), id(bus2.passenger), id(bus3.passenger))
print(bus1.passenger is bus2.passenger)
```

> 如果需要在类实例的方法或单一函数中传递可变类型，需要谨慎考虑传入的数据是否会做修改或者是否要独立保存数据

Last Modified 2022-07-20
