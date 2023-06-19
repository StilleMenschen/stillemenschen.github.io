# 类元编程

## 类工厂

```python
"""
record_factory: create simple classes just for holding data fields

    >>> Dog = record_factory('Dog', 'name weight owner')  # <1>
    >>> rex = Dog('Rex', 30, 'Bob')
    >>> rex  # <2>
    Dog(name='Rex', weight=30, owner='Bob')
    >>> name, weight, _ = rex  # <3>
    >>> name, weight
    ('Rex', 30)
    >>> "{2}'s dog weighs {1}kg".format(*rex)  # <4>
    "Bob's dog weighs 30kg"
    >>> rex.weight = 32  # <5>
    >>> rex
    Dog(name='Rex', weight=32, owner='Bob')
    >>> Dog.__mro__  # <6>
    (<class 'factories.Dog'>, <class 'object'>)


The factory also accepts a list or tuple of identifiers:

    >>> Dog = record_factory('Dog', ['name', 'weight', 'owner'])
    >>> Dog.__slots__
    ('name', 'weight', 'owner')

"""

from collections.abc import Iterable, Iterator
from typing import Union, Any

FieldNames = Union[str, Iterable[str]]  # <1>


def record_factory(cls_name: str, field_names: FieldNames) -> type[tuple]:  # <2>

    slots = parse_identifiers(field_names)  # <3>

    def __init__(self, *args, **kwargs) -> None:  # <4>
        attrs = dict(zip(self.__slots__, args))
        attrs.update(kwargs)
        for name, value in attrs.items():
            setattr(self, name, value)

    def __iter__(self) -> Iterator[Any]:  # <5>
        for name in self.__slots__:
            yield getattr(self, name)

    def __repr__(self):  # <6>
        values = ', '.join(f'{name}={value!r}'
                           for name, value in zip(self.__slots__, self))
        cls_name = self.__class__.__name__
        return f'{cls_name}({values})'

    cls_attrs = dict(  # <7>
        __slots__=slots,
        __init__=__init__,
        __iter__=__iter__,
        __repr__=__repr__,
    )

    return type(cls_name, (object,), cls_attrs)  # <8>


def parse_identifiers(names: FieldNames) -> tuple[str, ...]:
    if isinstance(names, str):
        names = names.replace(',', ' ').split()  # <9>
    if not all(s.isidentifier() for s in names):
        raise ValueError('names must all be valid identifiers')
    return tuple(names)
```

> 通过工厂函数创建的类不能序列化，即不能通过`pickle`模块里的`dump/load`函数处理

## 类型检查

```python
"""
A ``Checked`` subclass definition requires that keyword arguments are
used to create an instance, and provides a nice ``__repr__``::


    >>> class Movie(Checked):  # <1>
    ...     title: str  # <2>
    ...     year: int
    ...     box_office: float
    ...
    >>> movie = Movie(title='The Godfather', year=1972, box_office=137)  # <3>
    >>> movie.title
    'The Godfather'
    >>> movie  # <4>
    Movie(title='The Godfather', year=1972, box_office=137.0)


The type of arguments is runtime checked during instantiation
and when an attribute is set::


    >>> blockbuster = Movie(title='Avatar', year=2009, box_office='billions')
    Traceback (most recent call last):
      ...
    TypeError: 'billions' is not compatible with box_office:float
    >>> movie.year = 'MCMLXXII'
    Traceback (most recent call last):
      ...
    TypeError: 'MCMLXXII' is not compatible with year:int


Attributes not passed as arguments to the constructor are initialized with
default values::


    >>> Movie(title='Life of Brian')
    Movie(title='Life of Brian', year=0, box_office=0.0)


Providing extra arguments to the constructor is not allowed::

    >>> blockbuster = Movie(title='Avatar', year=2009, box_office=2000,
    ...                     director='James Cameron')
    Traceback (most recent call last):
      ...
    AttributeError: 'Movie' object has no attribute 'director'

Creating new attributes at runtime is restricted as well::

    >>> movie.director = 'Francis Ford Coppola'
    Traceback (most recent call last):
      ...
    AttributeError: 'Movie' object has no attribute 'director'

The `_asdict` instance method creates a `dict` from the attributes
of a `Movie` object::

    >>> movie._asdict()
    {'title': 'The Godfather', 'year': 1972, 'box_office': 137.0}

"""

from collections.abc import Callable  # <1>
from typing import Any, NoReturn, get_type_hints


class Field:
    def __init__(self, name: str, constructor: Callable) -> None:  # <2>
        # 检查构造函数
        if not callable(constructor) or constructor is type(None):  # <3>
            raise TypeError(f'{name!r} type hint must be callable')
        self.name = name
        self.constructor = constructor

    def __set__(self, instance: Any, value: Any) -> None:
        # 如果是内置对象 Ellipsis 则直接调用构造函数
        if value is ...:  # <4>
            value = self.constructor()
        else:
            try:
                value = self.constructor(value)  # <5>
            except (TypeError, ValueError) as e:  # <6>
                # 获得构造函数调用异常
                type_name = self.constructor.__name__
                msg = f'{value!r} is not compatible with {self.name}:{type_name}'
                # 声明异常的来源
                raise TypeError(msg) from e
        instance.__dict__[self.name] = value  # <7>

class Checked:
    @classmethod
    def _fields(cls) -> dict[str, type]:  # <1>
        return get_type_hints(cls)

    # 子类初始化时会调用此方法
    def __init_subclass__(subclass) -> None:  # <2>
        # 调用父级的方法
        super().__init_subclass__()           # <3>
        for name, constructor in subclass._fields().items():   # <4>
            setattr(subclass, name, Field(name, constructor))  # <5>

    def __init__(self, **kwargs: Any) -> None:
        for name in self._fields():             # <6>
            # 传入内置对象 Ellipsis 以区分 None 值和未提供值的情况
            value = kwargs.pop(name, ...)       # <7>
            setattr(self, name, value)          # <8>
        if kwargs:                              # <9>
            # 如果还有余项则说明存在与声明不匹配的字段名
            self.__flag_unknown_attrs(*kwargs)  # <10>


    def __setattr__(self, name: str, value: Any) -> None:  # <1>
        if name in self._fields():              # <2>
            cls = self.__class__
            descriptor = getattr(cls, name)
            # 一切设置实例属性的操作均被 __setattr__ 方法截获，即使存在注入 Field 之类的覆盖型描述符
            descriptor.__set__(self, value)     # <3>
        else:                                   # <4>
            self.__flag_unknown_attrs(name)

    def __flag_unknown_attrs(self, *names: str) -> NoReturn:  # <5>
        plural = 's' if len(names) > 1 else ''
        extra = ', '.join(f'{name!r}' for name in names)
        cls_name = repr(self.__class__.__name__)
        raise AttributeError(f'{cls_name} object has no attribute{plural} {extra}')

    def _asdict(self) -> dict[str, Any]:  # <6>
        return {
            name: getattr(self, name)
            for name, attr in self.__class__.__dict__.items()
            if isinstance(attr, Field)
        }

    def __repr__(self) -> str:  # <7>
        kwargs = ', '.join(
            f'{key}={value!r}' for key, value in self._asdict().items()
        )
        return f'{self.__class__.__name__}({kwargs})'

```

```python
"""
A ``Checked`` subclass definition requires that keyword arguments are
used to create an instance, and provides a nice ``__repr__``::


    >>> @checked
    ... class Movie:
    ...     title: str
    ...     year: int
    ...     box_office: float
    ...
    >>> movie = Movie(title='The Godfather', year=1972, box_office=137)
    >>> movie.title
    'The Godfather'
    >>> movie
    Movie(title='The Godfather', year=1972, box_office=137.0)


The type of arguments is runtime checked when an attribute is set,
including during instantiation::


    >>> blockbuster = Movie(title='Avatar', year=2009, box_office='billions')
    Traceback (most recent call last):
      ...
    TypeError: 'billions' is not compatible with box_office:float
    >>> movie.year = 'MCMLXXII'
    Traceback (most recent call last):
      ...
    TypeError: 'MCMLXXII' is not compatible with year:int


Attributes not passed as arguments to the constructor are initialized with
default values::


    >>> Movie(title='Life of Brian')
    Movie(title='Life of Brian', year=0, box_office=0.0)


Providing extra arguments to the constructor is not allowed::

    >>> blockbuster = Movie(title='Avatar', year=2009, box_office=2000,
    ...                     director='James Cameron')
    Traceback (most recent call last):
      ...
    AttributeError: 'Movie' has no attribute 'director'

Creating new attributes at runtime is restricted as well::

    >>> movie.director = 'Francis Ford Coppola'
    Traceback (most recent call last):
      ...
    AttributeError: 'Movie' has no attribute 'director'

The `_asdict` instance method creates a `dict` from the attributes
of a `Movie` object::

    >>> movie._asdict()
    {'title': 'The Godfather', 'year': 1972, 'box_office': 137.0}

"""

from collections.abc import Callable  # <1>
from typing import Any, NoReturn, get_type_hints


class Field:
    def __init__(self, name: str, constructor: Callable) -> None:  # <2>
        if not callable(constructor) or constructor is type(None):
            raise TypeError(f'{name!r} type hint must be callable')
        self.name = name
        self.constructor = constructor

    def __set__(self, instance: Any, value: Any) -> None:  # <3>
        if value is ...:  # <4>
            value = self.constructor()
        else:
            try:
                value = self.constructor(value)  # <5>
            except (TypeError, ValueError) as e:
                type_name = self.constructor.__name__
                msg = (
                    f'{value!r} is not compatible with {self.name}:{type_name}'
                )
                raise TypeError(msg) from e
        instance.__dict__[self.name] = value  # <6>


def checked(cls: type) -> type:  # <1>
    for name, constructor in _fields(cls).items():  # <2>
        setattr(cls, name, Field(name, constructor))  # <3>

    cls._fields = classmethod(_fields)  # type: ignore  # <4>

    instance_methods = (  # <5>
        __init__,
        __repr__,
        __setattr__,
        _asdict,
        __flag_unknown_attrs,
    )
    # 将当前模块中的方法添加到类中
    for method in instance_methods:  # <6>
        setattr(cls, method.__name__, method)

    return cls  # <7>


def _fields(cls: type) -> dict[str, type]:
    return get_type_hints(cls)


def __init__(self: Any, **kwargs: Any) -> None:
    for name in self._fields():
        value = kwargs.pop(name, ...)
        setattr(self, name, value)
    if kwargs:
        self.__flag_unknown_attrs(*kwargs)


def __setattr__(self: Any, name: str, value: Any) -> None:
    if name in self._fields():
        cls = self.__class__
        descriptor = getattr(cls, name)
        descriptor.__set__(self, value)
    else:
        self.__flag_unknown_attrs(name)


def __flag_unknown_attrs(self: Any, *names: str) -> NoReturn:
    plural = 's' if len(names) > 1 else ''
    extra = ', '.join(f'{name!r}' for name in names)
    cls_name = repr(self.__class__.__name__)
    raise AttributeError(f'{cls_name} has no attribute{plural} {extra}')


def _asdict(self: Any) -> dict[str, Any]:
    return {
        name: getattr(self, name)
        for name, attr in self.__class__.__dict__.items()
        if isinstance(attr, Field)
    }


def __repr__(self: Any) -> str:
    kwargs = ', '.join(
        f'{key}={value!r}' for key, value in self._asdict().items()
    )
    return f'{self.__class__.__name__}({kwargs})'
```

## 类的加载顺序

builderlib.py

```python
print('@ builderlib module start')


class Builder:  # <1>
    print('@ Builder body')

    def __init_subclass__(cls):  # <2>
        print(f'@ Builder.__init_subclass__({cls!r})')

        def inner_0(self):  # <3>
            print(f'@ SuperA.__init_subclass__:inner_0({self!r})')

        cls.method_a = inner_0

    def __init__(self):
        super().__init__()
        print(f'@ Builder.__init__({self!r})')


def deco(cls):  # <4>
    print(f'@ deco({cls!r})')

    def inner_1(self):  # <5>
        print(f'@ deco:inner_1({self!r})')

    cls.method_b = inner_1
    return cls  # <6>


class Descriptor:  # <1>
    print('@ Descriptor body')

    def __init__(self):  # <2>
        print(f'@ Descriptor.__init__({self!r})')

    def __set_name__(self, owner, name):  # <3>
        args = (self, owner, name)
        print(f'@ Descriptor.__set_name__{args!r}')

    def __set__(self, instance, value):  # <4>
        args = (self, instance, value)
        print(f'@ Descriptor.__set__{args!r}')

    def __repr__(self):
        return '<Descriptor instance>'


print('@ builderlib module end')
```

metalib.py

```python
print('% metalib module start')

import collections


class NosyDict(collections.UserDict):
    def __setitem__(self, key, value):
        args = (self, key, value)
        print(f'% NosyDict.__setitem__{args!r}')
        super().__setitem__(key, value)

    def __repr__(self):
        return '<NosyDict instance>'


class MetaKlass(type):
    print('% MetaKlass body')

    @classmethod  # <1>
    def __prepare__(meta_cls, cls_name, bases):  # <2>
        args = (meta_cls, cls_name, bases)
        print(f'% MetaKlass.__prepare__{args!r}')
        return NosyDict()  # <3>

    def __new__(meta_cls, cls_name, bases, cls_dict):  # <4>
        args = (meta_cls, cls_name, bases, cls_dict)
        print(f'% MetaKlass.__new__{args!r}')

        def inner_2(self):
            print(f'% MetaKlass.__new__:inner_2({self!r})')

        cls = super().__new__(meta_cls, cls_name, bases, cls_dict.data)  # <5>

        cls.method_c = inner_2  # <6>

        return cls  # <7>

    def __repr__(cls):  # <8>
        cls_name = cls.__name__
        return f"<class {cls_name!r} built by MetaKlass>"


print('% metalib module end')
```

evaldemo_meta.py

```python
#!/usr/bin/env python3

from builderlib import Builder, deco, Descriptor
from metalib import MetaKlass  # <1>

print('# evaldemo_meta module start')

@deco
class Klass(Builder, metaclass=MetaKlass):  # <2>
    print('# Klass body')

    attr = Descriptor()

    def __init__(self):
        super().__init__()
        print(f'# Klass.__init__({self!r})')

    def __repr__(self):
        return '<Klass instance>'


def main():
    obj = Klass()
    obj.method_a()
    obj.method_b()
    obj.method_c()  # <3>
    obj.attr = 999


if __name__ == '__main__':
    main()

print('# evaldemo_meta module end')
```

进入命令行执行导入`evaldemo_meta.py`和直接执行`python3 evaldemo_meta.py`将会有不同的加载顺序

```
>>> import evaldemo_meta
@ builderlib module start
@ Builder body
@ Descriptor body
@ builderlib module end
% metalib module start
% MetaKlass body
% metalib module end
# evaldemo_meta module start
% MetaKlass.__prepare__(<class 'metalib.MetaKlass'>, 'Klass', (<class 'builderlib.Builder'>,))
% NosyDict.__setitem__(<NosyDict instance>, '__module__', 'evaldemo_meta')
% NosyDict.__setitem__(<NosyDict instance>, '__qualname__', 'Klass')
# Klass body
@ Descriptor.__init__(<Descriptor instance>)
% NosyDict.__setitem__(<NosyDict instance>, 'attr', <Descriptor instance>)
% NosyDict.__setitem__(<NosyDict instance>, '__init__', <function Klass.__init__ at 0x000002302DBCE560>)
% NosyDict.__setitem__(<NosyDict instance>, '__repr__', <function Klass.__repr__ at 0x000002302DBCCA60>)
% NosyDict.__setitem__(<NosyDict instance>, '__classcell__', <cell at 0x000002302DBE17E0: empty>)
% MetaKlass.__new__(<class 'metalib.MetaKlass'>, 'Klass', (<class 'builderlib.Builder'>,), <NosyDict instance>)
@ Descriptor.__set_name__(<Descriptor instance>, <class 'Klass' built by MetaKlass>, 'attr')
@ Builder.__init_subclass__(<class 'Klass' built by MetaKlass>)
@ deco(<class 'Klass' built by MetaKlass>)
# evaldemo_meta module end
```

```
> python3 evaldemo_meta.py
@ builderlib module start
@ Builder body
@ Descriptor body
@ builderlib module end
% metalib module start
% MetaKlass body
% metalib module end
# evaldemo_meta module start
% MetaKlass.__prepare__(<class 'metalib.MetaKlass'>, 'Klass', (<class 'builderlib.Builder'>,))
% NosyDict.__setitem__(<NosyDict instance>, '__module__', '__main__')
% NosyDict.__setitem__(<NosyDict instance>, '__qualname__', 'Klass')
# Klass body
@ Descriptor.__init__(<Descriptor instance>)
% NosyDict.__setitem__(<NosyDict instance>, 'attr', <Descriptor instance>)
% NosyDict.__setitem__(<NosyDict instance>, '__init__', <function Klass.__init__ at 0x00000219F1EBD000>)
% NosyDict.__setitem__(<NosyDict instance>, '__repr__', <function Klass.__repr__ at 0x00000219F1EBD630>)
% NosyDict.__setitem__(<NosyDict instance>, '__classcell__', <cell at 0x00000219F1B4AB00: empty>)
% MetaKlass.__new__(<class 'metalib.MetaKlass'>, 'Klass', (<class 'builderlib.Builder'>,), <NosyDict instance>)
@ Descriptor.__set_name__(<Descriptor instance>, <class 'Klass' built by MetaKlass>, 'attr')
@ Builder.__init_subclass__(<class 'Klass' built by MetaKlass>)
@ deco(<class 'Klass' built by MetaKlass>)
@ Builder.__init__(<Klass instance>)
# Klass.__init__(<Klass instance>)
@ SuperA.__init_subclass__:inner_0(<Klass instance>)
@ deco:inner_1(<Klass instance>)
% MetaKlass.__new__:inner_2(<Klass instance>)
@ Descriptor.__set__(<Descriptor instance>, <Klass instance>, 999)
# evaldemo_meta module end
```

## 元类实现检查

```python
"""
A ``Checked`` subclass definition requires that keyword arguments are
used to create an instance, and provides a nice ``__repr__``::


    >>> class Movie(Checked):  # <1>
    ...     title: str  # <2>
    ...     year: int
    ...     box_office: float
    ...
    >>> movie = Movie(title='The Godfather', year=1972, box_office=137)  # <3>
    >>> movie.title
    'The Godfather'
    >>> movie  # <4>
    Movie(title='The Godfather', year=1972, box_office=137.0)


The type of arguments is runtime checked during instantiation
and when an attribute is set::


    >>> blockbuster = Movie(title='Avatar', year=2009, box_office='billions')
    Traceback (most recent call last):
      ...
    TypeError: 'billions' is not compatible with box_office:float
    >>> movie.year = 'MCMLXXII'
    Traceback (most recent call last):
      ...
    TypeError: 'MCMLXXII' is not compatible with year:int


Attributes not passed as arguments to the constructor are initialized with
default values::


    >>> Movie(title='Life of Brian')
    Movie(title='Life of Brian', year=0, box_office=0.0)


Providing extra arguments to the constructor is not allowed::

    >>> blockbuster = Movie(title='Avatar', year=2009, box_office=2000,
    ...                     director='James Cameron')
    Traceback (most recent call last):
      ...
    AttributeError: 'Movie' object has no attribute 'director'

Creating new attributes at runtime is restricted as well::

    >>> movie.director = 'Francis Ford Coppola'
    Traceback (most recent call last):
      ...
    AttributeError: 'Movie' object has no attribute 'director'

The `_asdict` instance method creates a `dict` from the attributes
of a `Movie` object::

    >>> movie._asdict()
    {'title': 'The Godfather', 'year': 1972, 'box_office': 137.0}

"""

from collections.abc import Callable
from typing import Any, NoReturn, get_type_hints

class Field:
    def __init__(self, name: str, constructor: Callable) -> None:
        if not callable(constructor) or constructor is type(None):
            raise TypeError(f'{name!r} type hint must be callable')
        self.name = name
        self.storage_name = '_' + name  # <1>
        self.constructor = constructor

    def __get__(self, instance, owner=None):
        if instance is None:  # <2>
            return self
        return getattr(instance, self.storage_name)  # <3>

    def __set__(self, instance: Any, value: Any) -> None:
        if value is ...:
            value = self.constructor()
        else:
            try:
                value = self.constructor(value)
            except (TypeError, ValueError) as e:
                type_name = self.constructor.__name__
                msg = f'{value!r} is not compatible with {self.name}:{type_name}'
                raise TypeError(msg) from e
        setattr(instance, self.storage_name, value)  # <4>

class CheckedMeta(type):

    def __new__(meta_cls, cls_name, bases, cls_dict):  # <1>
        if '__slots__' not in cls_dict:  # <2>
            slots = []
            type_hints = cls_dict.get('__annotations__', {})  # <3>
            for name, constructor in type_hints.items():   # <4>
                field = Field(name, constructor)  # <5>
                cls_dict[name] = field  # <6>
                slots.append(field.storage_name)  # <7>

            cls_dict['__slots__'] = slots  # <8>

        return super().__new__(
                meta_cls, cls_name, bases, cls_dict)  # <9>

class Checked(metaclass=CheckedMeta):
    __slots__ = ()  # skip CheckedMeta.__new__ processing

    @classmethod
    def _fields(cls) -> dict[str, type]:
        return get_type_hints(cls)

    def __init__(self, **kwargs: Any) -> None:
        for name in self._fields():
            value = kwargs.pop(name, ...)
            setattr(self, name, value)
        if kwargs:
            self.__flag_unknown_attrs(*kwargs)

    def __flag_unknown_attrs(self, *names: str) -> NoReturn:
        plural = 's' if len(names) > 1 else ''
        extra = ', '.join(f'{name!r}' for name in names)
        cls_name = repr(self.__class__.__name__)
        raise AttributeError(f'{cls_name} object has no attribute{plural} {extra}')

    def _asdict(self) -> dict[str, Any]:
        return {
            name: getattr(self, name)
            for name, attr in self.__class__.__dict__.items()
            if isinstance(attr, Field)
        }

    def __repr__(self) -> str:
        kwargs = ', '.join(
            f'{key}={value!r}' for key, value in self._asdict().items()
        )
        return f'{self.__class__.__name__}({kwargs})'

```

```python
import pytest

from checkedlib import Checked


def test_field_descriptor_validation_type_error():
    class Cat(Checked):
        name: str
        weight: float

    with pytest.raises(TypeError) as e:
        felix = Cat(name='Felix', weight=None)

    assert str(e.value) == 'None is not compatible with weight:float'


def test_field_descriptor_validation_value_error():
    class Cat(Checked):
        name: str
        weight: float

    with pytest.raises(TypeError) as e:
        felix = Cat(name='Felix', weight='half stone')

    assert str(e.value) == "'half stone' is not compatible with weight:float"


def test_constructor_attribute_error():
    class Cat(Checked):
        name: str
        weight: float

    with pytest.raises(AttributeError) as e:
        felix = Cat(name='Felix', weight=3.2, age=7)

    assert str(e.value) == "'Cat' object has no attribute 'age'"


def test_assignment_attribute_error():
    class Cat(Checked):
        name: str
        weight: float

    felix = Cat(name='Felix', weight=3.2)
    with pytest.raises(AttributeError) as e:
        felix.color = 'tan'

    assert str(e.value) == "'Cat' object has no attribute 'color'"


def test_field_invalid_constructor():
    with pytest.raises(TypeError) as e:
        class Cat(Checked):
            name: str
            weight: None

    assert str(e.value) == "'weight' type hint must be callable"
```

> `__prepare__`方法只在元类中有用，而且必须声明为类方法（添加`@classmethod`装饰器）。解释器调用元类的`__new__`方法之前会先调用`__prepare__`方法，使用类定义体中的属性创建映射。`__prepare__`方法的第一个参数是元类，随后两个参数分别是要构建的类名称和基类组成的元组，返回值必须是映射。元类构建新类时，`__prepare__`方法返回的映射会传给`__new__`方法的最后一个参数，然后再传给`__init__`方法。

- `cls.__bases__` 由类的基类组成的元组
- `cls.__qualname__` 获取类或函数的限定名称，即从全局模块的全局作用域到类的点分路径
- `cls.__subclasses__()` 返回一个列表，包含类的直接子类。这个方法的实现使用弱引用，防止在超类和子类（子类在`__bases__`属性中存储指向超类的强引用）之间出现循环引用。这个方法返回的列表是内存中现存的子类
- `cls.mro()` 构建类时，如果要获取存储在类属性`__mro__`中的超类元组，解释器会调用这个方法。元类可以覆盖这个方法，定制要构建的类解析方法的顺序。

Last Modified 2023-06-19
