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

## 策略模式延申

```python
# promotions.py

promos = []


def promotions(proms_func):
    promos.append(proms_func)
    return proms_func


@promotions
def fidelity_promo(order):
    """5% discount for customers with 1000 or more fidelity points"""
    return order.total() * .05 if order.customer.fidelity >= 1000 else 0


@promotions
def bulk_item_promo(order):
    """10% discount for each LineItem with 20 or more units"""
    discount = 0
    for item in order.cart:
        if item.quantity >= 20:
            discount += item.total() * .1
    return discount


@promotions
def large_order_promo(order):
    """7% discount for orders with 10 or more distinct items"""
    distinct_items = {item.product for item in order.cart}
    if len(distinct_items) >= 10:
        return order.total() * .07
    return 0


def best_promo(order):
    """Select best discount available
    """
    return max(promo(order) for promo in promos)
```

## 闭包

```python
"""
>>> avg = make_averager()
>>> avg(10)
10.0
>>> avg(11)
10.5
>>> avg(12)
11.0
>>> avg.__code__.co_varnames
('new_value', 'total')
>>> avg.__code__.co_freevars
('series',)
>>> avg.__closure__  # doctest: +ELLIPSIS
(<cell at 0x...: list object at 0x...>,)
>>> avg.__closure__[0].cell_contents
[10, 11, 12]
"""

DEMO = """
>>> avg.__closure__
(<cell at 0x107a44f78: list object at 0x107a91a48>,)
"""


def make_averager():
    series = []

    def averager(new_value):
        series.append(new_value)
        total = sum(series)
        return total / len(series)

    return averager
```

Last Modified 2022-07-18
