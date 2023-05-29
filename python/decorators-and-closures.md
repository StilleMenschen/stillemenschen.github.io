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
从外部调用

>>> from registration import *  # doctest: +ELLIPSIS
running register(active=False)->decorate(<function f1 at ...>)
running register(active=True)->decorate(<function f2 at ...>)
>>> register()(f3)  # doctest: +ELLIPSIS
running register(active=True)->decorate(<function f3 at ...>)
<function f3 at ...>
>>> registry  # doctest: +ELLIPSIS
{<function f3 at ...>, <function f2 at ...>}
>>> register(active=False)(f2)  # doctest: +ELLIPSIS
running register(active=False)->decorate(<function f2 at ...>)
<function f2 at ...>
>>> registry  # doctest: +ELLIPSIS
{<function f3 at ...>}
"""

# tag::REGISTRATION_PARAM[]

registry = set()  # <1>


def register(active=True):  # <2>
    def decorate(func):  # <3>
        print('running register'
              f'(active={active})->decorate({func})')
        if active:  # <4>
            registry.add(func)
        else:
            registry.discard(func)  # <5>

        return func  # <6>

    return decorate  # <7>


@register(active=False)  # <8>
def f1():
    print('running f1()')


@register()  # <9>
def f2():
    print('running f2()')


def f3():
    print('running f3()')

# end::REGISTRATION_PARAM[]
```

## 策略模式延申

```python
# strategy_best4.py
# Strategy pattern -- function-based implementation
# selecting the best promotion from list of functions
# registered by a decorator

"""
    >>> joe = Customer('John Doe', 0)
    >>> ann = Customer('Ann Smith', 1100)
    >>> cart = [LineItem('banana', 4, .5),
    ...         LineItem('apple', 10, 1.5),
    ...         LineItem('watermellon', 5, 5.0)]
    >>> Order(joe, cart, fidelity)
    <Order total: 42.00 due: 42.00>
    >>> Order(ann, cart, fidelity)
    <Order total: 42.00 due: 39.90>
    >>> banana_cart = [LineItem('banana', 30, .5),
    ...                LineItem('apple', 10, 1.5)]
    >>> Order(joe, banana_cart, bulk_item)
    <Order total: 30.00 due: 28.50>
    >>> long_order = [LineItem(str(item_code), 1, 1.0)
    ...               for item_code in range(10)]
    >>> Order(joe, long_order, large_order)
    <Order total: 10.00 due: 9.30>
    >>> Order(joe, cart, large_order)
    <Order total: 42.00 due: 42.00>


    >>> Order(joe, long_order, best_promo)
    <Order total: 10.00 due: 9.30>
    >>> Order(joe, banana_cart, best_promo)
    <Order total: 30.00 due: 28.50>
    >>> Order(ann, cart, best_promo)
    <Order total: 42.00 due: 39.90>

"""

from collections import namedtuple

Customer = namedtuple('Customer', 'name fidelity')


class LineItem:

    def __init__(self, product, quantity, price):
        self.product = product
        self.quantity = quantity
        self.price = price

    def total(self):
        return self.price * self.quantity


class Order:  # the Context

    def __init__(self, customer, cart, promotion=None):
        self.customer = customer
        self.cart = list(cart)
        self.promotion = promotion

    def total(self):
        if not hasattr(self, '__total'):
            self.__total = sum(item.total() for item in self.cart)
        return self.__total

    def due(self):
        if self.promotion is None:
            discount = 0
        else:
            discount = self.promotion(self)
        return self.total() - discount

    def __repr__(self):
        fmt = '<Order total: {:.2f} due: {:.2f}>'
        return fmt.format(self.total(), self.due())


promos = []  # <1>


def promotion(promo_func):  # <2>
    promos.append(promo_func)
    return promo_func


@promotion  # <3>
def fidelity(order):
    """5% discount for customers with 1000 or more fidelity points"""
    return order.total() * .05 if order.customer.fidelity >= 1000 else 0


@promotion
def bulk_item(order):
    """10% discount for each LineItem with 20 or more units"""
    discount = 0
    for item in order.cart:
        if item.quantity >= 20:
            discount += item.total() * .1
    return discount


@promotion
def large_order(order):
    """7% discount for orders with 10 or more distinct items"""
    distinct_items = {item.product for item in order.cart}
    if len(distinct_items) >= 10:
        return order.total() * .07
    return 0


def best_promo(order):  # <4>
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


class Averager():

    def __init__(self):
        self.series = []

    def __call__(self, new_value):
        self.series.append(new_value)
        total = sum(self.series)
        return total / len(self.series)


def make_averager():
    series = []

    def averager(new_value):
        series.append(new_value)
        total = sum(series)
        return total / len(series)

    return averager

def make_averager_variable():
    total = 0
    count = 0

    def averager(new_value):
        nonlocal total, count
        total += new_value
        count += 1
        return total / count

    return averager
```

## 带有参数的装饰器

```python
"""
>>> snooze(.1)  # doctest: +ELLIPSIS
[0.1...s] snooze(0.1) -> None
>>> clock('{name}: {elapsed}')(time.sleep)(.2)  # doctest: +ELLIPSIS
sleep: 0.20...
>>> clock('{name}({args}) dt={elapsed:0.3f}s')(time.sleep)(.2)  # doctest: +ELLIPSIS
sleep((0.2,)) dt=...s
"""

import functools
import time

DEFAULT_FMT = '[{elapsed:0.8f}s] {name}({arg_str}) -> {result}'


def clock(fmt=DEFAULT_FMT):
    def decorate(func):
        @functools.wraps(func)
        def clocked(*args, **kwargs):
            t0 = time.time()
            _result = func(*args, **kwargs)
            elapsed = time.time() - t0
            name = func.__name__
            arg_list = []
            if args:
                arg_list.append(', '.join(repr(arg) for arg in args))
            if kwargs:
                pairs = ['%s=%r' % (k, w) for k, w in sorted(kwargs.items())]
                arg_list.append(', '.join(pairs))
            arg_str = ', '.join(arg_list)
            result = repr(_result)
            print(fmt.format(**locals()))
            return _result

        return clocked

    return decorate


class clock01:  # <1>

    def __init__(self, fmt=DEFAULT_FMT):  # <2>
        self.fmt = fmt

    def __call__(self, func):  # <3>
        def clocked(*_args):
            t0 = time.perf_counter()
            _result = func(*_args)  # <4>
            elapsed = time.perf_counter() - t0
            name = func.__name__
            args = ', '.join(repr(arg) for arg in _args)
            result = repr(_result)
            print(self.fmt.format(**locals()))
            return _result

        return clocked


@clock()
def snooze(seconds):
    time.sleep(seconds)


if __name__ == '__main__':
    for i in range(3):
        snooze(.123)
```

## LRU 缓存

```python
"""
>>> fibonacci(6)
[0.00000000s] fibonacci(0) -> 0
[0.00000000s] fibonacci(1) -> 1
[0.00000000s] fibonacci(2) -> 1
[0.00000000s] fibonacci(3) -> 2
[0.00000000s] fibonacci(4) -> 3
[0.00000000s] fibonacci(5) -> 5
[0.00000000s] fibonacci(6) -> 8
8
"""

import time
import functools

DEFAULT_FMT = '[{elapsed:0.8f}s] {name}({arg_str}) -> {result}'


def clock(func):
    @functools.wraps(func)
    def clocked(*args, **kwargs):
        t0 = time.time()
        _result = func(*args, **kwargs)
        elapsed = time.time() - t0
        name = func.__name__
        arg_list = []
        if args:
            arg_list.append(', '.join(repr(arg) for arg in args))
        if kwargs:
            pairs = ['%s=%r' % (k, w) for k, w in sorted(kwargs.items())]
            arg_list.append(', '.join(pairs))
        arg_str = ', '.join(arg_list)
        result = repr(_result)
        print(DEFAULT_FMT.format(**locals()))
        return _result

    return clocked

# 叠放装饰器, 类似于 f = lru_cache(clock(func))
@functools.lru_cache()  # <1>
@clock  # <2>
def fibonacci(n: int) -> int:
    if n < 2:
        return n
    return fibonacci(n - 2) + fibonacci(n - 1)


if __name__ == '__main__':
    print(fibonacci(6))
```

## 泛函数

在单分派函数中尽量使用抽象基类，这样兼容的类型会多一些

```python
r"""
htmlize(): generic function example

# tag::HTMLIZE_DEMO[]

>>> htmlize({1, 2, 3})  # <1>
'<pre>{1, 2, 3}</pre>'
>>> htmlize(abs)
'<pre>&lt;built-in function abs&gt;</pre>'
>>> htmlize('Heimlich & Co.\n- a game')  # <2>
'<p>Heimlich &amp; Co.<br/>\n- a game</p>'
>>> htmlize(42)  # <3>
'<pre>42 (0x2a)</pre>'
>>> print(htmlize(['alpha', 66, {3, 2, 1}]))  # <4>
<ul>
<li><p>alpha</p></li>
<li><pre>66 (0x42)</pre></li>
<li><pre>{1, 2, 3}</pre></li>
</ul>
>>> htmlize(True)  # <5>
'<pre>True</pre>'
>>> htmlize(fractions.Fraction(2, 3))  # <6>
'<pre>2/3</pre>'
>>> htmlize(2/3)   # <7>
'<pre>0.6666666666666666 (2/3)</pre>'
>>> htmlize(decimal.Decimal('0.02380952'))
'<pre>0.02380952 (1/42)</pre>'

# end::HTMLIZE_DEMO[]
"""

# tag::HTMLIZE[]

from functools import singledispatch
from collections import abc
import fractions
import decimal
import html
import numbers

@singledispatch  # <1>
def htmlize(obj: object) -> str:
    content = html.escape(repr(obj))
    return f'<pre>{content}</pre>'

@htmlize.register  # <2>
def _(text: str) -> str:  # <3>
    content = html.escape(text).replace('\n', '<br/>\n')
    return f'<p>{content}</p>'

@htmlize.register  # <4>
def _(seq: abc.Sequence) -> str:
    inner = '</li>\n<li>'.join(htmlize(item) for item in seq)
    return '<ul>\n<li>' + inner + '</li>\n</ul>'

@htmlize.register  # <5>
def _(n: numbers.Integral) -> str:
    return f'<pre>{n} (0x{n:x})</pre>'

@htmlize.register  # <6>
def _(n: bool) -> str:
    return f'<pre>{n}</pre>'

@htmlize.register(fractions.Fraction)  # <7>
def _(x) -> str:
    frac = fractions.Fraction(x)
    return f'<pre>{frac.numerator}/{frac.denominator}</pre>'

@htmlize.register(decimal.Decimal)  # <8>
@htmlize.register(float)
def _(x) -> str:
    frac = fractions.Fraction(x).limit_denominator()
    return f'<pre>{x} ({frac.numerator}/{frac.denominator})</pre>'

# end::HTMLIZE[]
```

Last Modified 2023-05-29
