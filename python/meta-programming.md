# 元编程

## 动态读取属性

```python
"""
explore2.py: Script to explore the OSCON schedule feed

    >>> import json
    >>> raw_feed = json.load(open('data/osconfeed.json'))
    >>> feed = FrozenJSON(raw_feed)
    >>> len(feed.Schedule.speakers)
    357
    >>> sorted(feed.Schedule.keys())
    ['conferences', 'events', 'speakers', 'venues']
    >>> feed.Schedule.speakers[-1].name
    'Carina C. Zona'
    >>> talk = feed.Schedule.events[40]
    >>> talk.name
    'There *Will* Be Bugs'
    >>> talk.speakers
    [3471, 5199]
    >>> talk.flavor
    Traceback (most recent call last):
      ...
    KeyError: 'flavor'

"""

import keyword
from collections import abc


class FrozenJSON:
    """A read-only façade for navigating a JSON-like object
       using attribute notation
    """

    def __new__(cls, arg):  # <1>
        if isinstance(arg, abc.Mapping):
            # 如果是字典则调用父类构建，会执行 __init__
            return super().__new__(cls)  # <2>
        elif isinstance(arg, abc.MutableSequence):  # <3>
            # 如果是列表则逐个构建
            return [cls(item) for item in arg]
        else:
            return arg

    def __init__(self, mapping):
        self.__data = {}
        for key, value in mapping.items():
            # 针对关键字设置下划线
            if keyword.iskeyword(key):
                key += '_'
            self.__data[key] = value

    def __getattr__(self, name):
        try:
            return getattr(self.__data, name)
        except AttributeError:
            # 递归动态构建属性
            return FrozenJSON(self.__data[name])  # <4>

    def __dir__(self):
        # 列出当前拥有的键
        return self.__data.keys()
```

对应的<a href="/python/osconfeed.json.gz">JSON 数据</a>

## 属性读取缓存

```python
"""
schedule_v5.py: cached properties using functools

    >>> event = Record.fetch('event.33950')
    >>> event
    <Event 'There *Will* Be Bugs'>
    >>> event.venue
    <Record serial=1449>
    >>> event.venue_serial
    1449
    >>> event.venue.name
    'Portland 251'

    >>> for spkr in event.speakers:  # <3>
    ...     print(f'{spkr.serial}: {spkr.name}')
    ...
    3471: Anna Martelli Ravenscroft
    5199: Alex Martelli

"""

import inspect
import json
from functools import cached_property, cache

JSON_PATH = 'data/osconfeed.json'


class Record:
    __index = None

    def __init__(self, **kwargs):
        self.__dict__.update(kwargs)

    def __repr__(self):
        return f'<{self.__class__.__name__} serial={self.serial!r}>'

    @staticmethod
    def fetch(key):
        if Record.__index is None:
            Record.__index = load()
        return Record.__index[key]


class Event(Record):

    def __repr__(self):
        try:
            return f'<{self.__class__.__name__} {self.name!r}>'
        except AttributeError:
            return super().__repr__()

    @cached_property
    def venue(self):
        key = f'venue.{self.venue_serial}'
        # 避免同名属性覆盖
        return self.__class__.fetch(key)

    # 类似于 speakers = property(cache(speakers))
    @property  # <1>
    @cache  # <2>
    def speakers(self):
        spkr_serials = self.__dict__['speakers']
        # 避免同名属性覆盖
        fetch = self.__class__.fetch
        return [fetch(f'speaker.{key}')
                for key in spkr_serials]


def load(path=JSON_PATH):
    records = {}
    with open(path) as fp:
        raw_data = json.load(fp)
    for collection, raw_records in raw_data['Schedule'].items():
        record_type = collection[:-1]
        cls_name = record_type.capitalize()
        # 从全局对象中获取类型，如果获取不到则使用 Record 类型
        cls = globals().get(cls_name, Record)
        # 判断获取到的对象为类且是 Record 的子类
        if inspect.isclass(cls) and issubclass(cls, Record):
            factory = cls
        else:
            factory = Record
        for raw_record in raw_records:
            key = f'{record_type}.{raw_record["serial"]}'
            records[key] = factory(**raw_record)
    return records
```

cached_property() 的设定与 property() 有所不同。 常规的 property 会阻止属性写入，除非定义了 setter。 与之相反，cached_property 则允许写入。

cached_property 装饰器仅在执行查找且不存在同名属性时才会运行。 当运行时，cached_property 会写入同名的属性。 后续的属性读取和写入操作会优先于 cached_property 方法，其行为就像普通的属性一样。

缓存的值可通过删除该属性来清空。 这允许 cached_property 方法再次运行。详见[官方文档](https://docs.python.org/zh-cn/3/library/functools.html#functools.cached_property)

```python
import pytest

import schedule_v5 as schedule

@pytest.fixture
def records():
    yield schedule.load(schedule.JSON_PATH)


def test_load(records):
    assert len(records) == 895


def test_record_attr_access():
    rec = schedule.Record(spam=99, eggs=12)
    assert rec.spam == 99
    assert rec.eggs == 12


def test_venue_record(records):
    venue = records['venue.1469']
    assert venue.serial == 1469
    assert venue.name == 'Exhibit Hall C'


def test_fetch_speaker_record():
    speaker = schedule.Record.fetch('speaker.3471')
    assert speaker.name == 'Anna Martelli Ravenscroft'


def test_event_type():
    event = schedule.Record.fetch('event.33950')
    assert type(event) is schedule.Event
    assert repr(event) == "<Event 'There *Will* Be Bugs'>"


def test_event_repr():
    event = schedule.Record.fetch('event.33950')
    assert repr(event) == "<Event 'There *Will* Be Bugs'>"
    event2 = schedule.Event(serial=77, kind='show')
    assert repr(event2) == '<Event serial=77>'


def test_event_venue():
    event = schedule.Record.fetch('event.33950')
    assert event.venue_serial == 1449
    assert event.venue == schedule.Record.fetch('venue.1449')
    assert event.venue.name == 'Portland 251'


def test_event_speakers():
    event = schedule.Record.fetch('event.33950')
    assert len(event.speakers) == 2
    anna, alex = [schedule.Record.fetch(f'speaker.{s}') for s in (3471, 5199)]
    assert event.speakers == [anna, alex]

def test_event_no_speakers():
    event = schedule.Record.fetch('event.36848')
    assert event.speakers == []
```

## 特性覆盖实例属性

```python
"""
>>> obj = Class()
>>> vars(obj)  # vars 返回 __dict__
{}
>>> obj.data
'the class data attr'
>>> obj.data = 'bar'  # 遮盖类属性
>>> vars(obj)
{'data': 'bar'}
>>> obj.data
'bar'
>>> Class.data  # 类属性没有变化
'the class data attr'
>>> Class.prop  # doctest: +ELLIPSIS
<property object at ...>
>>> obj.prop
'the prop value'
>>> obj.prop = 'foo'  # doctest: +ELLIPSIS
Traceback (most recent call last):
  ...
AttributeError: property 'prop' of 'Class' object has no setter
>>> obj.__dict__['prop'] = 'foo'  # 设置实例的属性
>>> vars(obj)
{'data': 'bar', 'prop': 'foo'}
>>> obj.prop  # 没有覆盖类特性
'the prop value'
>>> Class.prop  # doctest: +ELLIPSIS
<property object at ...>
>>> Class.prop = 'baz'  # 覆盖类特性
>>> obj.prop  # 这时是实例的属性
'foo'
>>> obj.data
'bar'
>>> Class.data
'the class data attr'
>>> Class.data = property(lambda self: 'the "data" prop value')  # 特性覆盖类的属性
>>> obj.data  # 实例的属性被特性覆盖
'the "data" prop value'
>>> del Class.data  # 删除类的特性
>>> obj.data  # 现在是实例的属性
'bar'
"""


class Class:
    data = 'the class data attr'

    @property
    def prop(self):
        return 'the prop value'
```

## 特性替代属性

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
    >>> sorted(vars(nutmeg).items())  # <2>
    [('description', 'Moluccan nutmeg'), ('price', 13.95), ('weight', 8)]

"""


def quantity(storage_name):  # <1>

    def qty_getter(instance):  # <2>
        # 防止无限递归
        return instance.__dict__[storage_name]  # <3>

    def qty_setter(instance, value):  # <4>
        if value > 0:
            # 防止无限递归
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

## 特性的文档

```python
"""
Example of property documentation

    >>> f = Foo()
    >>> f.bar = 77
    >>> f.bar
    77
    >>> Foo.bar.__doc__
    'The bar attribute'
"""


class Foo:

    @property
    def bar(self):
        '''The bar attribute'''
        return self.__dict__['bar']

    @bar.setter
    def bar(self, value):
        self.__dict__['bar'] = value
```

## 可删除的特性

```python
"""
This class is inspired by the Black Knight scene in the movie
"Monty Python and the Holy Grail", where King Arthur fights the
Black Knight, slicing off his arms and legs, but the knight
refuses to concede defeat.

# BEGIN BLACK_KNIGHT_DEMO
    >>> knight = BlackKnight()
    >>> knight.member
    next member is:
    'an arm'
    >>> del knight.member
    BLACK KNIGHT (loses an arm)
    -- 'Tis but a scratch.
    >>> del knight.member
    BLACK KNIGHT (loses another arm)
    -- It's just a flesh wound.
    >>> del knight.member
    BLACK KNIGHT (loses a leg)
    -- I'm invincible!
    >>> del knight.member
    BLACK KNIGHT (loses another leg)
    -- All right, we'll call it a draw.

# END BLACK_KNIGHT_DEMO
"""


class BlackKnight:

    def __init__(self):
        self.members = ['an arm', 'another arm',
                        'a leg', 'another leg']
        self.phrases = ["'Tis but a scratch.",
                        "It's just a flesh wound.",
                        "I'm invincible!",
                        "All right, we'll call it a draw."]

    @property
    def member(self):
        print('next member is:')
        return self.members[0]

    @member.deleter
    def member(self):
        text = 'BLACK KNIGHT (loses {})\n-- {}'
        print(text.format(self.members.pop(0), self.phrases.pop(0)))
```

## 特殊函数或属性

在用户定义的类中，以下特殊方法用于获取、设直、删除和列山属性。

使用点号表示法或内置的函数 getattr，hasattr 和 setattr 存取属性都会触发相应的特殊方法。但是，直接通过实例的`__dict__`属性读写属性不会触发这些特殊方法，必要时，这是绕过特殊方法的常用方式。

- `__class__`

  对象所属类的引用（例如， `obj.__class__` 与 type(obj) 的作用相同）。Python 的某些特殊方法（例如 `__getattr__`），只在对象的类中而不在实例中寻找。

- `__dict__`

  存储对象或类的可写属性的映射。有`__dict__`属性的对象，任何时候都能随意设置新属性。对于有`__slots__`属性的类，其实例可能没有`__dict__`属性。

- `__slots__`

  类可以定义这个属性，节省内存。`__slots__`属性的值是一个字符串元组，列出了允许有的属性。如果 `__slots__` 中没有 `__dict__`，那么该类的实例就没有`__dict__`属性，而只允许有`__slots__`中列出的属性。

- `dir([object])`

  列出对象的大多数属性。官方文档说，dir 函数的目的是交互式使用，因此没有提供完整的属性列表，只列出了一组“重要的”属性名。dir 函数能审查有或没有`__dict__`属性的对象。dir 函数不列出`__dict__`属性本身，但会列出其中的键。实现特殊方法`__dir__`可以自定义 dir 函数的输出。如果没有指定可选的 object 参数，则 dir 函数会列出当前作用域中的名称。

- `getattr(object, name [, default])`

  从 object 对象中获取 name 字符串对应的属性。主要用于获取事先不知道名称的属性（或方法）。获取的属性可能来自对象所属的类或超类。如果指定的属性不存在，则 getattr 函数会抛出 `AttributeError` 异常，或者返回 default 参数的值（如果提供了的话）。在标准库中， cmd 包的 Cmd.onecmd 方法就很好地利用了 gettatr，用于获取并执行用户定义的命令。

- `hasattr(object ,name)`

  如果 object 对象中存在指定的属性，或者能以某种方式（例如继承）通过 object 对象获取指定的属性，那么就返回 True。Python 标准库文档指出：“这个函数的实现方法是调用 getattr(object, name) 函数，看看是否抛出`AttributeError`异常。”

- `setattr(objec, name, value)`

  把 object 对象指定属性的值设为 value，前提是 object 对象能接受提供的值。这可能创建一个新属性，也可能覆盖现有属性。

- `vars([object])`

  返回 object 对象的`__dict__`属性。如果实例所属的类定义了`__slots__` 属性且实例没有`__dict__`属性，那么 vars 函数就不能处理（相反，dir 函数能处理）这样的实例。如果没有指定参数，那么 vars() 函数的作用与 locals() 函数一样：返回表示本地作用域的字典。

- `__delattr__(self, name)`

  只要使用 del 语句删除属性,就会调用这个方法。例如,del obj.attr 语句触发 `Class.__delattr_(obj,'attr')`。如果 attr 是一个特性,而且类实现了 `__delattr__` 方法,则永不调用特性的删值方法。

- `__dir__(self)`

  在对象上调用 dir 函数时会调用这个方法,以列出属性。例如, dir(obj) 触发 `Class.__dir__(obj)`。所有现代的 Python 控制台在 Tab 补全时也使用该方法。

- `_getattr__(self, name)`

  仅当获取指定的属性失败,搜索过 obj、Class 及其超类之后会调用这个方法。表达式 `obj.no_such_attr`、 `getattr(obj, 'no_such_attr')` 和 `hasattr(obj, 'no_such_attr')` 可能触发 `Class.__getattr__(obj, 'no_such_attr')` ,但是,仅当在 obj、 Class 及其超类中找不到指定的属性时才触发。

- `__getattribute__(self, name)`

  在 Python 代码中尝试直接获取指定名称的属性时始终调用这个方法［某些情况下(例如获取 `__repr__`方法时)解释器可能会绕过该方法］。点号表示法与内置函数 getattr 和 hasattr 会触发这个方法。`__getattr__`仅在`__getattribute__`之后,而且仅当 `__getattribute__` 抛出 AttributeError 时调用。为了在获取 obj 实例的属性时不导致无限递归, `__getattribute__` 方法的实现要使用 `super().__getattribute__(obj, name)`

- `__setattr__(self, name, value)`

  尝试设置指定名称的属性时总会调用这个方法。点号表示法和内置函数 setattr 会触发这个方法。例如, obj.attr ＝ 42 和 `setattr(obj, 'attr', 42)` 都会触发 `Class.__setattr__(obj, 'attr', 42)`。

> 对于自定义类来说，如果隐式调用特殊方法，仅当特殊方法在对象所属的类型上定义，而不是在对象的实例字典中定义时，才能确保调用成功。

Last Modified 2023-06-17
