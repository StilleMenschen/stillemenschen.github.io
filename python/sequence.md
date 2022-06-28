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

Last Modified 2022-06-28
