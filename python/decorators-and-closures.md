# 装饰器和闭包

## 计算函数执行时间

```python
from time import time
from copy import deepcopy
from functools import wraps
from multiprocessing import Pool


def timer(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        start_time = time() * 1000
        result = func(*args, **kwargs)
        end_time = time() * 1000
        duration_time = end_time - start_time
        print(f'execute time running {func.__name__}: {duration_time:.2f} ms')
        return result

    return wrapper


@timer
def f1(a):
    return 1 + a[0]


@timer
def f2(a):
    return sum(a)


@timer
def f3(a):
    basic = deepcopy(a)
    for i in a:
        for j in basic:
            pass
    return basic


def main():
    n = 1e3
    source = list()
    while n > 0:
        source.append(n)
        n -= 1
    with Pool(processes=3) as pool:
        pool.apply_async(func=f1, args=(source,))
        pool.apply_async(func=f2, args=(source,))
        pool.apply_async(func=f3, args=(source,))
        pool.close()
        pool.join()


if __name__ == '__main__':
    main()
```

## 装饰器的调用时机

```python
"""
    >>> import registration
    running register(<function f1 at 0x00000150CF843760>)
    running register(<function f2 at 0x00000150CF842200>)
    >>> registration.register
    [<function f1 at 0x00000150CF843760>, <function f2 at 0x00000150CF842200>]
"""


registry = []  # <1>


def register(func):  # <2>
    print('running register(%s)' % func)  # <3>
    registry.append(func)  # <4>
    return func  # <5>


@register  # <6>
def f1():
    print('running f1()')


@register
def f2():
    print('running f2()')


def f3():  # <7>
    print('running f3()')


def main():  # <8>
    print('running main()')
    print('registry ->', registry)
    f1()
    f2()
    f3()


if __name__ == '__main__':
    main()  # <9>
```

Last Modified 2022-07-15