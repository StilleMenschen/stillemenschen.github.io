# 函数与设计模式

## 策略模式

### 经典实现

```python
# classic_strategy.py
# Strategy pattern -- classic implementation

"""

    >>> joe = Customer('John Doe', 0)  # <1>
    >>> ann = Customer('Ann Smith', 1100)
    >>> cart = (LineItem('banana', 4, Decimal('.5')),  # <2>
    ...         LineItem('apple', 10, Decimal('1.5')),
    ...         LineItem('watermelon', 5, Decimal(5)))
    >>> Order(joe, cart, FidelityPromo())  # <3>
    <Order total: 42.00 due: 42.00>
    >>> Order(ann, cart, FidelityPromo())  # <4>
    <Order total: 42.00 due: 39.90>
    >>> banana_cart = (LineItem('banana', 30, Decimal('.5')),  # <5>
    ...                LineItem('apple', 10, Decimal('1.5')))
    >>> Order(joe, banana_cart, BulkItemPromo())  # <6>
    <Order total: 30.00 due: 28.50>
    >>> long_cart = tuple(LineItem(str(sku), 1, Decimal(1)) # <7>
    ...                  for sku in range(10))
    >>> Order(joe, long_cart, LargeOrderPromo())  # <8>
    <Order total: 10.00 due: 9.30>
    >>> Order(joe, cart, LargeOrderPromo())
    <Order total: 42.00 due: 42.00>

"""

from abc import ABC, abstractmethod
from collections.abc import Sequence
from decimal import Decimal
from typing import NamedTuple, Optional


class Customer(NamedTuple):
    name: str
    fidelity: int


class LineItem(NamedTuple):
    product: str
    quantity: int
    price: Decimal

    def total(self) -> Decimal:
        return self.price * self.quantity


class Order(NamedTuple):  # the Context
    customer: Customer
    cart: Sequence[LineItem]
    promotion: Optional['Promotion'] = None

    def total(self) -> Decimal:
        totals = (item.total() for item in self.cart)
        return sum(totals, start=Decimal(0))

    def due(self) -> Decimal:
        if self.promotion is None:
            discount = Decimal(0)
        else:
            discount = self.promotion.discount(self)
        return self.total() - discount

    def __repr__(self):
        return f'<Order total: {self.total():.2f} due: {self.due():.2f}>'


class Promotion(ABC):  # the Strategy: an abstract base class
    @abstractmethod
    def discount(self, order: Order) -> Decimal:
        """Return discount as a positive dollar amount"""


class FidelityPromo(Promotion):  # first Concrete Strategy
    """5% discount for customers with 1000 or more fidelity points"""

    def discount(self, order: Order) -> Decimal:
        rate = Decimal('0.05')
        if order.customer.fidelity >= 1000:
            return order.total() * rate
        return Decimal(0)


class BulkItemPromo(Promotion):  # second Concrete Strategy
    """10% discount for each LineItem with 20 or more units"""

    def discount(self, order: Order) -> Decimal:
        discount = Decimal(0)
        for item in order.cart:
            if item.quantity >= 20:
                discount += item.total() * Decimal('0.1')
        return discount


class LargeOrderPromo(Promotion):  # third Concrete Strategy
    """7% discount for orders with 10 or more distinct items"""

    def discount(self, order: Order) -> Decimal:
        distinct_items = {item.product for item in order.cart}
        if len(distinct_items) >= 10:
            return order.total() * Decimal('0.07')
        return Decimal(0)
```

### 函数实现

```python
# strategy.py
# Strategy pattern -- function-based implementation

"""

    >>> joe = Customer('John Doe', 0)  # <1>
    >>> ann = Customer('Ann Smith', 1100)
    >>> cart = [LineItem('banana', 4, Decimal('.5')),
    ...         LineItem('apple', 10, Decimal('1.5')),
    ...         LineItem('watermelon', 5, Decimal(5))]
    >>> Order(joe, cart, fidelity_promo)  # <2>
    <Order total: 42.00 due: 42.00>
    >>> Order(ann, cart, fidelity_promo)
    <Order total: 42.00 due: 39.90>
    >>> banana_cart = [LineItem('banana', 30, Decimal('.5')),
    ...                LineItem('apple', 10, Decimal('1.5'))]
    >>> Order(joe, banana_cart, bulk_item_promo)  # <3>
    <Order total: 30.00 due: 28.50>
    >>> long_cart = [LineItem(str(item_code), 1, Decimal(1))
    ...               for item_code in range(10)]
    >>> Order(joe, long_cart, large_order_promo)
    <Order total: 10.00 due: 9.30>
    >>> Order(joe, cart, large_order_promo)
    <Order total: 42.00 due: 42.00>

"""

from collections.abc import Sequence
from dataclasses import dataclass
from decimal import Decimal
from typing import Optional, Callable, NamedTuple


class Customer(NamedTuple):
    name: str
    fidelity: int


class LineItem(NamedTuple):
    product: str
    quantity: int
    price: Decimal

    def total(self):
        return self.price * self.quantity


@dataclass(frozen=True)
class Order:  # the Context
    customer: Customer
    cart: Sequence[LineItem]
    promotion: Optional[Callable[['Order'], Decimal]] = None  # <1>

    def total(self) -> Decimal:
        totals = (item.total() for item in self.cart)
        return sum(totals, start=Decimal(0))

    def due(self) -> Decimal:
        if self.promotion is None:
            discount = Decimal(0)
        else:
            discount = self.promotion(self)  # <2>
        return self.total() - discount

    def __repr__(self):
        return f'<Order total: {self.total():.2f} due: {self.due():.2f}>'


# <3>


def fidelity_promo(order: Order) -> Decimal:  # <4>
    """5% discount for customers with 1000 or more fidelity points"""
    if order.customer.fidelity >= 1000:
        return order.total() * Decimal('0.05')
    return Decimal(0)


def bulk_item_promo(order: Order) -> Decimal:
    """10% discount for each LineItem with 20 or more units"""
    discount = Decimal(0)
    for item in order.cart:
        if item.quantity >= 20:
            discount += item.total() * Decimal('0.1')
    return discount


def large_order_promo(order: Order) -> Decimal:
    """7% discount for orders with 10 or more distinct items"""
    distinct_items = {item.product for item in order.cart}
    if len(distinct_items) >= 10:
        return order.total() * Decimal('0.07')
    return Decimal(0)
```

>使用函数实现，减少了创建类所需要的开销，需要应用具体策略时直接将函数传入

### 验证策略模式

```python
from decimal import Decimal

import pytest  # type: ignore

from strategy import Customer, LineItem, Order
from strategy import fidelity_promo, bulk_item_promo, large_order_promo


@pytest.fixture
def customer_fidelity_0() -> Customer:
    return Customer('John Doe', 0)


@pytest.fixture
def customer_fidelity_1100() -> Customer:
    return Customer('Ann Smith', 1100)


@pytest.fixture
def cart_plain() -> tuple[LineItem, ...]:
    return (
        LineItem('banana', 4, Decimal('0.5')),
        LineItem('apple', 10, Decimal('1.5')),
        LineItem('watermelon', 5, Decimal('5.0')),
    )


def test_fidelity_promo_no_discount(customer_fidelity_0, cart_plain) -> None:
    order = Order(customer_fidelity_0, cart_plain, fidelity_promo)
    assert order.total() == 42
    assert order.due() == 42


def test_fidelity_promo_with_discount(customer_fidelity_1100, cart_plain) -> None:
    order = Order(customer_fidelity_1100, cart_plain, fidelity_promo)
    assert order.total() == 42
    assert order.due() == Decimal('39.9')


def test_bulk_item_promo_no_discount(customer_fidelity_0, cart_plain) -> None:
    order = Order(customer_fidelity_0, cart_plain, bulk_item_promo)
    assert order.total() == 42
    assert order.due() == 42


def test_bulk_item_promo_with_discount(customer_fidelity_0) -> None:
    cart = [LineItem('banana', 30, Decimal('0.5')),
            LineItem('apple', 10, Decimal('1.5'))]
    order = Order(customer_fidelity_0, cart, bulk_item_promo)
    assert order.total() == 30
    assert order.due() == Decimal('28.5')


def test_large_order_promo_no_discount(customer_fidelity_0, cart_plain) -> None:
    order = Order(customer_fidelity_0, cart_plain, large_order_promo)
    assert order.total() == 42
    assert order.due() == 42


def test_large_order_promo_with_discount(customer_fidelity_0) -> None:
    cart = [LineItem(str(item_code), 1, Decimal(1))
            for item_code in range(10)]
    order = Order(customer_fidelity_0, cart, large_order_promo)
    assert order.total() == 10
    assert order.due() == Decimal('9.3')
```

### 最佳策略模式

独立的策略模块

```python
# promotions.py

from decimal import Decimal

from strategy import Order


def fidelity_promo(order: Order) -> Decimal:  # <3>
    """5% discount for customers with 1000 or more fidelity points"""
    if order.customer.fidelity >= 1000:
        return order.total() * Decimal('0.05')
    return Decimal(0)


def bulk_item_promo(order: Order) -> Decimal:
    """10% discount for each LineItem with 20 or more units"""
    discount = Decimal(0)
    for item in order.cart:
        if item.quantity >= 20:
            discount += item.total() * Decimal('0.1')
    return discount


def large_order_promo(order: Order) -> Decimal:
    """7% discount for orders with 10 or more distinct items"""
    distinct_items = {item.product for item in order.cart}
    if len(distinct_items) >= 10:
        return order.total() * Decimal('0.07')
    return Decimal(0)
```

订单和客户的类，引用策略模块，循环所有策略找出可以获得最大折扣的策略并返回折扣金额

```python
# strategy_best4.py
# Strategy pattern -- function-based implementation
# selecting the best promotion from list of functions
# registered by a decorator

"""
    >>> from decimal import Decimal
    >>> from strategy import Customer, LineItem, Order
    >>> from promotions import *
    >>> joe = Customer('John Doe', 0)
    >>> ann = Customer('Ann Smith', 1100)
    >>> cart = [LineItem('banana', 4, Decimal('.5')),
    ...         LineItem('apple', 10, Decimal('1.5')),
    ...         LineItem('watermelon', 5, Decimal(5))]
    >>> Order(joe, cart, fidelity_promo)
    <Order total: 42.00 due: 42.00>
    >>> Order(ann, cart, fidelity_promo)
    <Order total: 42.00 due: 39.90>
    >>> banana_cart = [LineItem('banana', 30, Decimal('.5')),
    ...                LineItem('apple', 10, Decimal('1.5'))]
    >>> Order(joe, banana_cart, bulk_item_promo)
    <Order total: 30.00 due: 28.50>
    >>> long_cart = [LineItem(str(item_code), 1, Decimal(1))
    ...               for item_code in range(10)]
    >>> Order(joe, long_cart, large_order_promo)
    <Order total: 10.00 due: 9.30>
    >>> Order(joe, cart, large_order_promo)
    <Order total: 42.00 due: 42.00>


    >>> Order(joe, long_cart, best_promo)
    <Order total: 10.00 due: 9.30>
    >>> Order(joe, banana_cart, best_promo)
    <Order total: 30.00 due: 28.50>
    >>> Order(ann, cart, best_promo)
    <Order total: 42.00 due: 39.90>

"""

from decimal import Decimal
from typing import Callable

# 订单的定义类在上面的文件 strategy.py 中
from strategy import Order

Promotion = Callable[[Order], Decimal]

promos: list[Promotion] = []  # <1>


def promotion(promo: Promotion) -> Promotion:  # <2>
    promos.append(promo)
    return promo


def best_promo(order: Order) -> Decimal:
    """Compute the best discount available"""
    return max(promo(order) for promo in promos)  # <3>


@promotion  # <4>
def fidelity(order: Order) -> Decimal:
    """5% discount for customers with 1000 or more fidelity points"""
    if order.customer.fidelity >= 1000:
        return order.total() * Decimal('0.05')
    return Decimal(0)


@promotion
def bulk_item(order: Order) -> Decimal:
    """10% discount for each LineItem with 20 or more units"""
    discount = Decimal(0)
    for item in order.cart:
        if item.quantity >= 20:
            discount += item.total() * Decimal('0.1')
    return discount


@promotion
def large_order(order: Order) -> Decimal:
    """7% discount for orders with 10 or more distinct items"""
    distinct_items = {item.product for item in order.cart}
    if len(distinct_items) >= 10:
        return order.total() * Decimal('0.07')
    return Decimal(0)
```

## 命令模式

```python
class MacroCommands:

    def __init__(self, commands: list | tuple):
        self.commands = list(commands)

    def __call__(self, *args, **kwargs):
        for command in self.commands:
            result = command()
            print(command.__name__, '->', result)


def fibonacci(n=42, first=0, second=1):
    if n <= 1:
        return first
    return fibonacci(n - 1, second, first + second)


cmds = MacroCommands([
    lambda: sum(range(101)),
    fibonacci
])

cmds()
```

Last Modified 2023-05-30
