# 属性描述符

## 通过描述符定义类属性

```python
"""
A line item for a bulk food order has description, weight and price fields::

    >>> raisins = LineItem('Golden raisins', 10, 6.95)
    >>> raisins.weight, raisins.description, raisins.price
    (10, 'Golden raisins', 6.95)

A ``subtotal`` method gives the total price for that line item::

    >>> raisins.subtotal()
    69.5

The weight of a ``LineItem`` must be greater than 0::

    >>> raisins.weight = -20
    Traceback (most recent call last):
        ...
    ValueError: value must be > 0

Negative or 0 price is not acceptable either::

    >>> truffle = LineItem('White truffle', 100, 0)
    Traceback (most recent call last):
        ...
    ValueError: value must be > 0


No change was made::

    >>> raisins.weight
    10

"""


class Quantity:  # <1>

    def __init__(self, storage_name):
        self.storage_name = storage_name  # <2>

    def __set__(self, instance, value):  # <3>
        if value > 0:
            instance.__dict__[self.storage_name] = value  # <4>
        else:
            raise ValueError('value must be > 0')


class LineItem:
    weight = Quantity('weight')  # <5>
    price = Quantity('price')  # <6>

    def __init__(self, description, weight, price):  # <7>
        self.description = description
        self.weight = weight
        self.price = price

    def subtotal(self):
        return self.weight * self.price
```

## 不指定名称的托管属性

```python
"""

A line item for a bulk food order has description, weight and price fields::

    >>> raisins = LineItem('Golden raisins', 10, 6.95)
    >>> raisins.weight, raisins.description, raisins.price
    (10, 'Golden raisins', 6.95)

A ``subtotal`` method gives the total price for that line item::

    >>> raisins.subtotal()
    69.5

The weight of a ``LineItem`` must be greater than 0::

    >>> raisins.weight = -20
    Traceback (most recent call last):
        ...
    ValueError: value must be > 0

No change was made::

    >>> raisins.weight
    10

The value of the attributes managed by the descriptors are stored in
alternate attributes, created by the descriptors in each ``LineItem``
instance::

    >>> raisins = LineItem('Golden raisins', 10, 6.95)
    >>> dir(raisins)  # doctest: +ELLIPSIS +NORMALIZE_WHITESPACE
    ['_Quantity#0', '_Quantity#1', '__class__', ...
     'description', 'price', 'subtotal', 'weight']
    >>> getattr(raisins, '_Quantity#0')
    10
    >>> getattr(raisins, '_Quantity#1')
    6.95

If the descriptor is accessed in the class, the descriptor object is
returned:

    >>> LineItem.weight  # doctest: +ELLIPSIS
    <...Quantity object at 0x...>
    >>> LineItem.weight.storage_name
    '_Quantity#0'

"""


class Quantity:
    __counter = 0

    def __init__(self):
        cls = self.__class__
        prefix = cls.__name__
        index = cls.__counter
        self.storage_name = '_{}#{}'.format(prefix, index)
        # 累加计数, 确保新的属性名不会被覆盖(注意这里是使用了类本身而不是实例)
        cls.__counter += 1

    def __get__(self, instance, owner):
        if instance is None:
            return self  # <1>
        else:
            return getattr(instance, self.storage_name)  # <2>

    def __set__(self, instance, value):
        if value > 0:
            setattr(instance, self.storage_name, value)
        else:
            raise ValueError('value must be > 0')


class LineItem:
    weight = Quantity()
    price = Quantity()

    def __init__(self, description, weight, price):
        self.description = description
        self.weight = weight
        self.price = price

    def subtotal(self):
        return self.weight * self.price
```

## 特性工厂函数

```python
"""

A line item for a bulk food order has description, weight and price fields::

    >>> raisins = LineItem('Golden raisins', 10, 6.95)
    >>> raisins.weight, raisins.description, raisins.price
    (10, 'Golden raisins', 6.95)

A ``subtotal`` method gives the total price for that line item::

    >>> raisins.subtotal()
    69.5

The weight of a ``LineItem`` must be greater than 0::

    >>> raisins.weight = -20
    Traceback (most recent call last):
        ...
    ValueError: value must be > 0

No change was made::

    >>> raisins.weight
    10

The value of the attributes managed by the descriptors are stored in
alternate attributes, created by the descriptors in each ``LineItem``
instance::

    >>> raisins = LineItem('Golden raisins', 10, 6.95)
    >>> dir(raisins)  # doctest: +ELLIPSIS +NORMALIZE_WHITESPACE
    [... '_quantity:0', '_quantity:1', 'description',
    'price', 'subtotal', 'weight']
    >>> getattr(raisins, '_quantity:0')
    10
    >>> getattr(raisins, '_quantity:1')
    6.95

"""


def quantity():  # <1>
    try:
        quantity.counter += 1  # <2>
    except AttributeError:
        quantity.counter = 0  # <3>

    storage_name = '_{}:{}'.format('quantity', quantity.counter)  # <4>

    def qty_getter(instance):  # <5>
        return getattr(instance, storage_name)

    def qty_setter(instance, value):
        if value > 0:
            setattr(instance, storage_name, value)
        else:
            raise ValueError('value must be > 0')

    return property(qty_getter, qty_setter)


class LineItem:
    weight = quantity()
    price = quantity()

    def __init__(self, description, weight, price):
        self.description = description
        self.weight = weight
        self.price = price

    def subtotal(self):
        return self.weight * self.price
```

Last Modified 2022-08-07