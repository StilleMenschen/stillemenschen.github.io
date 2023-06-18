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
    ValueError: weight must be > 0

Negative or 0 price is not acceptable either::

    >>> truffle = LineItem('White truffle', 100, 0)
    Traceback (most recent call last):
        ...
    ValueError: price must be > 0

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
            msg = f'{self.storage_name} must be > 0'
            raise ValueError(msg)

    def __get__(self, instance, owner):  # <5>
        return instance.__dict__[self.storage_name]



class LineItem:
    weight = Quantity('weight')  # <1>
    price = Quantity('price')  # <2>

    def __init__(self, description, weight, price):  # <3>
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
    ValueError: weight must be > 0

No change was made::

    >>> raisins.weight
    10

Negative or 0 price is not acceptable either::

    >>> truffle = LineItem('White truffle', 100, 0)
    Traceback (most recent call last):
        ...
    ValueError: price must be > 0

If the descriptor is accessed in the class, the descriptor object is
returned:

    >>> LineItem.weight  # doctest: +ELLIPSIS
    <...Quantity object at 0x...>
    >>> LineItem.weight.storage_name
    'weight'

"""


class Quantity:

    def __set_name__(self, owner, name):  # <1>
        self.storage_name = name  # <2>

    def __set__(self, instance, value):  # <3>
        if value > 0:
            instance.__dict__[self.storage_name] = value
        else:
            msg = f'{self.storage_name} must be > 0'
            raise ValueError(msg)


class LineItem:
    weight = Quantity()  # <2>
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

The value of the attributes managed by the properties are stored in
instance attributes, created in each ``LineItem`` instance::

    >>> nutmeg = LineItem('Moluccan nutmeg', 8, 13.95)
    >>> nutmeg.weight, nutmeg.price  # <1>
    (8, 13.95)
    >>> nutmeg.__dict__  # <2>
    {'description': 'Moluccan nutmeg', 'weight': 8, 'price': 13.95}


"""


def quantity(storage_name):  # <1>

    def qty_getter(instance):  # <2>
        return instance.__dict__[storage_name]  # <3>

    def qty_setter(instance, value):  # <4>
        if value > 0:
            instance.__dict__[storage_name] = value  # <5>
        else:
            raise ValueError('value must be > 0')

    return property(qty_getter, qty_setter)  # <6>


class LineItem:
    weight = quantity('weight')  # <1>
    price = quantity('price')  # <2>

    def __init__(self, description, weight, price):
        self.description = description
        self.weight = weight  # <3>
        self.price = price

    def subtotal(self):
        return self.weight * self.price  # <4>
```

## 允许自定义校验的描述符

```python
"""model_v5.py
"""
import abc


class Validated(abc.ABC):

    def __set_name__(self, owner, name):
        self.storage_name = name

    def __set__(self, instance, value):
        value = self.validate(self.storage_name, value)  # <1>
        instance.__dict__[self.storage_name] = value  # <2>

    @abc.abstractmethod
    def validate(self, name, value):  # <3>
        """return validated value or raise ValueError"""


class Quantity(Validated):
    """a number greater than zero"""

    def validate(self, name, value):  # <1>
        if value <= 0:
            raise ValueError(f'{name} must be > 0')
        return value


class NonBlank(Validated):
    """a string with at least one non-space character"""

    def validate(self, name, value):
        value = value.strip()
        if not value:  # <2>
            raise ValueError(f'{name} cannot be blank')
        return value  # <3>
```

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
    ValueError: weight must be > 0

No change was made::

    >>> raisins.weight
    10

Negative or 0 price is not acceptable either::

    >>> truffle = LineItem('White truffle', 100, 0)
    Traceback (most recent call last):
        ...
    ValueError: price must be > 0

If the descriptor is accessed in the class, the descriptor object is
returned:

    >>> LineItem.weight  # doctest: +ELLIPSIS
    <model_v5.Quantity object at 0x...>
    >>> LineItem.weight.storage_name
    'weight'

The `NonBlank` descriptor prevents empty or blank strings to be used
for the description:

    >>> br_nuts = LineItem('Brazil Nuts', 10, 34.95)
    >>> br_nuts.description = ' '
    Traceback (most recent call last):
        ...
    ValueError: description cannot be blank
    >>> void = LineItem('', 1, 1)
    Traceback (most recent call last):
        ...
    ValueError: description cannot be blank


"""

import model_v5 as model  # <1>


class LineItem:
    description = model.NonBlank()  # <2>
    weight = model.Quantity()
    price = model.Quantity()

    def __init__(self, description, weight, price):
        self.description = description
        self.weight = weight
        self.price = price

    def subtotal(self):
        return self.weight * self.price
```

## 覆盖型与非覆盖型描述符

```python
"""
Overriding descriptor (a.k.a. data descriptor or enforced descriptor):

    >>> obj = Model()
    >>> obj.over  # doctest: +ELLIPSIS
    Overriding.__get__() invoked with args:
        self     = <descriptorkinds.Overriding object at 0x...>
        instance = <descriptorkinds.Model object at 0x...>
        owner    = <class 'descriptorkinds.Model'>
    >>> Model.over  # doctest: +ELLIPSIS
    Overriding.__get__() invoked with args:
        self     = <descriptorkinds.Overriding object at 0x...>
        instance = None
        owner    = <class 'descriptorkinds.Model'>


An overriding descriptor cannot be shadowed by assigning to an instance:

    >>> obj = Model()
    >>> obj.over = 7  # doctest: +ELLIPSIS
    Overriding.__set__() invoked with args:
        self     = <descriptorkinds.Overriding object at 0x...>
        instance = <descriptorkinds.Model object at 0x...>
        value    = 7
    >>> obj.over  # doctest: +ELLIPSIS
    Overriding.__get__() invoked with args:
        self     = <descriptorkinds.Overriding object at 0x...>
        instance = <descriptorkinds.Model object at 0x...>
        owner    = <class 'descriptorkinds.Model'>


Not even by poking the attribute into the instance ``__dict__``:

    >>> obj.__dict__['over'] = 8
    >>> obj.over  # doctest: +ELLIPSIS
    Overriding.__get__() invoked with args:
        self     = <descriptorkinds.Overriding object at 0x...>
        instance = <descriptorkinds.Model object at 0x...>
        owner    = <class 'descriptorkinds.Model'>
    >>> vars(obj)
    {'over': 8}

Overriding descriptor without ``__get__``:

    >>> obj.over_no_get  # doctest: +ELLIPSIS
    <descriptorkinds.OverridingNoGet object at 0x...>
    >>> Model.over_no_get   # doctest: +ELLIPSIS
    <descriptorkinds.OverridingNoGet object at 0x...>
    >>> obj.over_no_get = 7  # doctest: +ELLIPSIS
    OverridingNoGet.__set__() invoked with args:
        self     = <descriptorkinds.OverridingNoGet object at 0x...>
        instance = <descriptorkinds.Model object at 0x...>
        value    = 7
    >>> obj.over_no_get  # doctest: +ELLIPSIS
    <descriptorkinds.OverridingNoGet object at 0x...>


Poking the attribute into the instance ``__dict__`` means you can read the new
value for the attribute, but setting it still triggers ``__set__``:

    >>> obj.__dict__['over_no_get'] = 9
    >>> obj.over_no_get
    9
    >>> obj.over_no_get = 7  # doctest: +ELLIPSIS
    OverridingNoGet.__set__() invoked with args:
        self     = <descriptorkinds.OverridingNoGet object at 0x...>
        instance = <descriptorkinds.Model object at 0x...>
        value    = 7
    >>> obj.over_no_get
    9


Non-overriding descriptor (a.k.a. non-data descriptor or shadowable descriptor):

    >>> obj = Model()
    >>> obj.non_over  # doctest: +ELLIPSIS
    NonOverriding.__get__() invoked with args:
        self     = <descriptorkinds.NonOverriding object at 0x...>
        instance = <descriptorkinds.Model object at 0x...>
        owner    = <class 'descriptorkinds.Model'>
    >>> Model.non_over  # doctest: +ELLIPSIS
    NonOverriding.__get__() invoked with args:
        self     = <descriptorkinds.NonOverriding object at 0x...>
        instance = None
        owner    = <class 'descriptorkinds.Model'>


A non-overriding descriptor can be shadowed by assigning to an instance:

    >>> obj.non_over = 7
    >>> obj.non_over
    7


Methods are non-over descriptors:

    >>> obj.spam  # doctest: +ELLIPSIS
    <bound method Model.spam of <descriptorkinds.Model object at 0x...>>
    >>> Model.spam  # doctest: +ELLIPSIS
    <function Model.spam at 0x...>
    >>> obj.spam()  # doctest: +ELLIPSIS
    Model.spam() invoked with arg:
        self = <descriptorkinds.Model object at 0x...>
    >>> obj.spam = 7
    >>> obj.spam
    7


No descriptor type survives being overwritten on the class itself:

    >>> Model.over = 1
    >>> obj.over
    1
    >>> Model.over_no_get = 2
    >>> obj.over_no_get
    2
    >>> Model.non_over = 3
    >>> obj.non_over
    7

"""


def print_args(name, *args):  # <1>
    cls_name = args[0].__class__.__name__
    arg_names = ['self', 'instance', 'owner']
    if name == 'set':
        arg_names[-1] = 'value'
    print('{}.__{}__() invoked with args:'.format(cls_name, name))
    for arg_name, value in zip(arg_names, args):
        print('    {:8} = {}'.format(arg_name, value))


class Overriding:  # <2>
    """a.k.a. data descriptor or enforced descriptor"""

    def __get__(self, instance, owner):
        print_args('get', self, instance, owner)  # <3>

    def __set__(self, instance, value):
        print_args('set', self, instance, value)


class OverridingNoGet:  # <4>
    """an overriding descriptor without ``__get__``"""

    def __set__(self, instance, value):
        print_args('set', self, instance, value)


class NonOverriding:  # <5>
    """a.k.a. non-data or shadowable descriptor"""

    def __get__(self, instance, owner):
        print_args('get', self, instance, owner)


class Model:  # <6>
    over = Overriding()
    over_no_get = OverridingNoGet()
    non_over = NonOverriding()

    def spam(self):  # <7>
        print('Model.spam() invoked with arg:')
        print('    self =', self)
```

### 覆盖型描述符

实现`__set__`方法的描述符属于覆盖型描述符，因为虽然描述符是类属性，但是实现`__set__`方法的话，描述符将覆盖对实例属性的赋值操作。特性也是覆盖型描述符：如果没有提供设值函数，那么 property 类中的`__set__`方法就会抛出 AttributeError 异常，表明那个属性是只读的。

特性和其他覆盖型描述符（例如 Django 模型字段）既实现`__set__`方法，也买现`__get__`方法，不过也可以只实现`__set__`方法。此时，只有写操作由描述符处理。通过实例读取描述符会返回描述符对象本身，因为没有处理读操作的`__get__`方法。如果直接通过实例的`__dict__`属性创建同名实例属性，那么以后再设置那个属性时，仍由`__set__`方法插手接管，但是读取那个属性的话，会直接从实例中返回新赋予的值，而不返回描述符对象。也就是说，实例属性会遮盖描述符，不过只有读操作是如此。

### 非覆盖型描述符

没有实现`__set__`方法的描述符是非覆盖型描述符。如果设置了同名的实例属性，那么描述符就会被遮盖，致使其无法处理那个实例的那个属性。所有方法和`＠functools.cached_property`是以非覆盖型描述符实现的。

> 不管描述符是不是覆盖型，为类属性赋值都能覆盖描述符（猴子补丁）

## 方法是描述符

在类中定义的函数，如果在实例上调用，就会变成绑定方法，因为用户定义的函数都有`__get__`方法，在依附到类上后，就相当于描述符。绑定方法对象还有`__call__`方法，用于处理实际调用过程。这个方法会调用`__func__`属性引用的原始函数，传入的第一个参数是方法的`__self__`属性。这就是形参 self 的隐式绑定方式。函数会变成绑定方法，这是 Python 语言底层使用描述符的最好例证。

```python
"""

    >>> word = Text('forward')
    >>> word  # <1>
    Text('forward')
    >>> word.reverse()  # <2>
    Text('drawrof')
    >>> Text.reverse(Text('backward'))  # <3>
    Text('drawkcab')
    >>> type(Text.reverse), type(word.reverse)  # <4>
    (<class 'function'>, <class 'method'>)
    >>> list(map(Text.reverse, ['repaid', (10, 20, 30), Text('stressed')]))  # <5>
    ['diaper', (30, 20, 10), Text('desserts')]
    >>> Text.reverse.__get__(word)  # <6>
    <bound method Text.reverse of Text('forward')>
    >>> Text.reverse.__get__(None, Text)  # doctest: +ELLIPSIS
    <function Text.reverse at 0x...>
    >>> word.reverse  # <8>
    <bound method Text.reverse of Text('forward')>
    >>> word.reverse.__self__  # <9>
    Text('forward')
    >>> word.reverse.__func__ is Text.reverse  # <10>
    True

"""

import collections


class Text(collections.UserString):

    def __repr__(self):
        return 'Text({!r})'.format(self.data)

    def reverse(self):
        return self[::-1]
```

## 描述符用法

- 使用 property 以保持简单

内置的 property 类创建的其实是实现了`__set__`方法和`__get__`方法的覆盖型描述符，即使没有定义设值方法。特性的`__set__`方法会默认抛出 AttributeError: can't set attribute，因此创建只读属性最简单的方式是使用特性，这能避免下一条所述的问题。

- 只读描述符必须有`__set__`方法

使用描述符类实现只读属性时要记住，`__get__`和`__set__`这两个方法必须都定义，否则，实例的同名属性会遮盖描述符。只读属性的`__set__`方法只需抛出 AttributeError 异常，并提供合适的错误消息。

- 用于验证的描述符可以只有`__set__`方法

在仅用于验证的描述符中，`__set__`方法应该检查 value 参数获得的值，如果有效，就使用描述符实例的名称作为键，直接在实例的`__dict__`属性中设置。这样，从实例中读取同名属性的速度很快，因为不用经过`__get__`方法处理。

- 仅有`__get__`方法的描述符可以实现高效缓存

如果只编写了`__get__`方法，那么得到的是非覆盖型描述符。这种描述符可用于执行某些耗费资源的计算，然后为实例设置同名属性，缓存结果。同名实例属性会遮盖描述符，因此后续访问直接从实例的`__dict__`属性中获取值，不再触发描述符的`__get__`方法。`＠functools.cached_property` 装饰器创建的其实就是非覆盖型描述符。

- 非特殊的方法可以被实例属性遮盖

函数和方法只实现了`__get__`方法，属于非覆盖型描述符。像 my\*obj.the_method ＝ 7 这样简单赋值之后，后续通过该实例访问 the_method，得到的是数值 7 —— 但是不影响类或其他实例。然而，特殊方法不受这个问题的影响。解释器只在类中寻找特殊方法，也就是说， `repr(x)` 执行的其实是 `x.__class__.__repr__(x)`，因此 x 的`__repr__`属性对 `repr(x)`方法调用没有影响。出于同样的原因，实例的`__getattr__`属性不会破坏常规的属性访问规则。

Last Modified 2023-06-18
