# 序列

## 命名元组

```python
from collections import namedtuple

City = namedtuple('City', 'name country population coordinates')

print(City._fields)

metro_areas = [
    ('Tokyo', 'JP', 36.933, (35.689722, 139.691667)),
    ('Delhi NCR', 'IN', 21.935, (28.613889, 77.208889)),
    ('Mexico City', 'MX', 20.142, (19.433333, -99.133333)),
    ('New York-Newark', 'US', 20.104, (40.808611, -74.020386)),
    ('Sao Paulo', 'BR', 19.649, (-23.547778, -46.635833)),
]

for metro in metro_areas:
    c = City._make(metro)
    print(c)
    print(c._asdict())
```

## 切片

```python
invoice = """
0.....6.................................40........52...55........
1909 Pimoroni PiBrella                      $17.50    3    $52.50
1489 6mm Tactile Switch x20                  $4.95    2    $9.90
1510 Panavise Jr. - PV-201                  $28.00    1    $28.00
1601 PiTFT Mini Kit 320x240                 $34.95    1    $34.95
"""

SKU = slice(0, 6)
DESCRIPTION = slice(6, 40)
UNIT_PRICE = slice(40, 52)
QUANTITY = slice(52, 55)
ITEM_TOTAL = slice(55, None)

line_items = invoice.split('\n')[2:]

for item in line_items:
    print(item[UNIT_PRICE], item[DESCRIPTION])

numbers = list(range(10))
numbers[2:5] = [20, 30]
print(numbers)
del numbers[5:7]
print(numbers)
numbers[3::2] = [11, 22]
print(numbers)
numbers[2:5] = [100]
print(numbers)
```

| 运算       | 结果：                      |
| ---------- | --------------------------- |
| `s[i]`     | s 的第 i 项，起始为 0       |
| `s[i:j]`   | s 从 i 到 j 的切片          |
| `s[i:j:k]` | s 从 i 到 j 步长为 k 的切片 |

- 如果 i 或 j 为负值，则索引顺序是相对于序列 s 的末尾: 索引号会被替换为 len(s) + i 或 len(s) + j。 但要注意 -0 仍然为 0。

- s 从 i 到 j 的切片被定义为所有满足 i <= k < j 的索引号 k 的项组成的序列。 如果 i 或 j 大于 len(s)，则使用 len(s)。 如果 i 被省略或为 None，则使用 0。 如果 j 被省略或为 None，则使用 len(s)。 如果 i 大于等于 j，则切片为空。

- s 从 i 到 j 步长为 k 的切片被定义为所有满足 `0 <= n < (j-i)/k` 的索引号 `x = i + n*k` 的项组成的序列。 换句话说，索引号为 `i, i+k, i+2*k, i+3*k`，以此类推，当达到 j 时停止 (但一定不包括 j)。 当 k 为正值时，i 和 j 会被减至不大于 len(s)。 当 k 为负值时，i 和 j 会被减至不大于 len(s) - 1。 如果 i 或 j 被省略或为 None，它们会成为“终止”值 (是哪一端的终止值则取决于 k 的符号)。 请注意，k 不可为零。 如果 k 为 None，则当作 1 处理。

## 加乘运算

```python
numbers = [1, 2, 3]
print(numbers * 3)
strings = 'abc'
print(strings * 3)

board = [['_'] * 3 for i in range(3)]
print(board)
board[2][0] = 'X'
print(board)

board = [['_'] * 3] * 3  # 错误, 获得的是引用
print(board)
board[2][0] = 'X'
print(board)

print(id(numbers))
numbers *= 3
print(numbers, id(numbers))

numbers = (1, 2, 3)
print(id(numbers))
numbers *= 2
print(numbers, id(numbers))

numbers = (1, 2, [10, 20])
try:
    # tuple 是不可变的, 但 list 可以改变
    numbers[2] += [30, 40]
except TypeError:
    # 虽然会抛出异常, 但其实第三个可变的list数据还是被修改了
    print('TypeError')
finally:
    print(numbers)
```

> 不要将可变对象放在元组中；增量赋值操作不是一个原子操作

## 保持顺序添加

```python
import bisect
import random


def grade(score, breakpoints=(60, 70, 80, 90,), grades='FDCBA'):
    i = bisect.bisect(breakpoints, score)
    return grades[i]


for v in [33, 89, 100, 63, 75]:
    print(f'{v} is {grade(v)}')

random.seed(42)
SIZE = 10
arr = []
for _ in range(SIZE):
    value = random.randrange(SIZE * 2)
    bisect.insort(arr, value)
    print(f'{value:2d} -> {arr}')
```

## 数值数组

```python
import os
from array import array
from random import uniform
import numpy

floats = array('d', (uniform(0, 1000) for _ in range(10 ** 3)))
f = open('floats.bin', 'wb')
print(floats[-10:])
floats.tofile(f)
f.close()

fp = open('floats.bin', 'rb')
floats2 = array('d')
floats2.fromfile(fp, 10 ** 3)
fp.close()
print(floats2[-10:])

print(floats2 == floats)

os.remove('floats.bin')

numbers = array('h', [-2, -1, 0, 1, 2])
memv = memoryview(numbers)
memv_oct = memv.cast('B')
print(memv_oct.tolist())
memv_oct[5] = 4
print(numbers)

np_array = numpy.arange(12)
print(type(np_array))
print(np_array.shape)
np_array.shape = 3, 4
print(np_array)
print(np_array[:, 1])
print(np_array.transpose())
```

## 拆包

```python
def fun(a, b, c, d, *others):
    return a, b, c, d, others


print(fun(*[1, 2], 3, *range(4, 7)))
```

## 双向队列

```python
from collections import deque

dq = deque(range(10), maxlen=10)
dq.rotate(3)
print(dq)
dq.rotate(-4)
print(dq)
dq.appendleft(-1)
print(dq)
dq.extend([11, 22, 33])
print(dq)
dq.extendleft([10, 20, 30, 40])
print(dq)
```

## 排序

请注意，如果`__gt__()` 没有实现，< 可以退回到使用 `__lt__()`

```python
class Student:
    def __init__(self, name, grade, age):
        self.name = name
        self.grade = grade
        self.age = age

    def __repr__(self):
        return repr((self.name, self.grade, self.age))


student_objects = [
    Student('john', 'A', 15),
    Student('jane', 'B', 12),
    Student('dave', 'B', 10),
]

Student.__lt__ = lambda self, other: self.age < other.age
print(sorted(student_objects))

students = ['dave', 'john', 'jane']
new_grades = {'john': 'F', 'jane': 'A', 'dave': 'C'}
print(sorted(students, key=new_grades.__getitem__))
```

## 匹配句法

```python
"""
metro_lat_long.py

Demonstration of nested tuple unpacking::

    >>> main()
                    |  latitude | longitude
    Mexico City     |   19.4333 |  -99.1333
    New York-Newark |   40.8086 |  -74.0204
    São Paulo       |  -23.5478 |  -46.6358

"""

# tag::MAIN[]
metro_areas = [
    ('Tokyo', 'JP', 36.933, (35.689722, 139.691667)),
    ('Delhi NCR', 'IN', 21.935, (28.613889, 77.208889)),
    ('Mexico City', 'MX', 20.142, (19.433333, -99.133333)),
    ('New York-Newark', 'US', 20.104, (40.808611, -74.020386)),
    ('São Paulo', 'BR', 19.649, (-23.547778, -46.635833)),
]


def describe_list(lst):
    match lst:
        case []:
            return "This is an empty list."
        case [1, 2, *rest]:
            return f"The first two items are 1 and 2. There are {len(rest)} more items."
        case [_, *rest, 10]:
            return f"The list ends with a 10. There are {len(rest)} items before it."
        case _:
            return "This is an arbitrary list."


def main():
    print(f'{"":15} | {"latitude":>9} | {"longitude":>9}')
    for record in metro_areas:
        match record:  # <1>
            case [name, _, _, (lat, lon)] if lon <= 0:  # <2>
                print(f'{name:15} | {lat:9.4f} | {lon:9.4f}')


# end::MAIN[]

if __name__ == '__main__':
    main()

```

Last Modified 2023-05-23
