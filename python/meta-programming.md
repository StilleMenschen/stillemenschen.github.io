# 元编程

## 动态读取属性

```python
"""
osconfeed.py: Script to download the OSCON schedule feed


    >>> feed = load()  # <1>
    >>> sorted(feed['Schedule'].keys())  # <2>
    ['conferences', 'events', 'speakers', 'venues']
    >>> for key, value in sorted(feed['Schedule'].items()):
    ...     print('{:3} {}'.format(len(value), key))  # <3>
    ...
      1 conferences
    485 events
    354 speakers
     53 venues
    >>> feed['Schedule']['speakers'][-1]['name']  # <4>
    'Carina C. Zona'
    >>> feed['Schedule']['speakers'][-1]['serial']  # <5>
    141590
    >>> feed['Schedule']['events'][40]['name']
    'There *Will* Be Bugs'
    >>> feed['Schedule']['events'][40]['speakers']  # <6>
    [3471, 5199]


"""

from urllib.request import urlopen
import warnings
import os
import json

URL = 'https://raw.githubusercontent.com/pythonprobr/oscon2014/master/schedule/osconfeed.json'
JSON = 'data/osconfeed.json'


def load():
    if not os.path.exists(JSON):
        msg = 'downloading {} to {}'.format(URL, JSON)
        warnings.warn(msg)  # <1>
        with urlopen(URL) as remote, open(JSON, 'wb') as local:  # <2>
            local.write(remote.read())

    with open(JSON) as fp:
        return json.load(fp)  # <3>
```

```python
"""
explore2.py: Script to explore the OSCON schedule feed

    >>> from osconfeed import load
    >>> raw_feed = load()
    >>> feed = FrozenJSON(raw_feed)
    >>> len(feed.Schedule.speakers)
    354
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

from collections import abc
from keyword import iskeyword


class FrozenJSON:
    """A read-only façade for navigating a JSON-like object
       using attribute notation
    """

    def __new__(cls, arg):  # <1>
        if isinstance(arg, abc.Mapping):
            return super().__new__(cls)  # <2>
        elif isinstance(arg, abc.MutableSequence):  # <3>
            return [cls(item) for item in arg]
        else:
            return arg

    def __init__(self, mapping):
        self.__data = {}
        for key, value in mapping.items():
            # 如果是关键字则加上特殊的后缀, 如 item.class_
            if iskeyword(key):
                key += '_'
            self.__data[key] = value

    def __getattr__(self, name):
        if hasattr(self.__data, name):
            return getattr(self.__data, name)
        else:
            return FrozenJSON(self.__data[name])  # <4>
```

对应的<a href="/python/osconfeed.json.zip">JSON 数据</a>

## shelve 存储数据

```python
"""
schedule2.py: traversing OSCON schedule data

    >>> import shelve
    >>> db = shelve.open(DB_NAME)
    >>> if CONFERENCE not in db: load_db(db)


    >>> DbRecord.set_db(db)  # <1>
    >>> event = DbRecord.fetch('event.33950')  # <2>
    >>> event  # <3>
    <Event 'There *Will* Be Bugs'>
    >>> event.venue  # <4>
    <DbRecord serial='venue.1449'>
    >>> event.venue.name  # <5>
    'Portland 251'
    >>> for spkr in event.speakers:  # <6>
    ...     print('{0.serial}: {0.name}'.format(spkr))
    ...
    speaker.3471: Anna Martelli Ravenscroft
    speaker.5199: Alex Martelli


    >>> db.close()

"""

import warnings
import inspect  # <1>

import osconfeed

DB_NAME = 'data/schedule2_db'  # <2>
CONFERENCE = 'conference.115'


class Record:
    def __init__(self, **kwargs):
        self.__dict__.update(kwargs)

    def __eq__(self, other):  # <3>
        if isinstance(other, Record):
            return self.__dict__ == other.__dict__
        else:
            return NotImplemented


class MissingDatabaseError(RuntimeError):
    """Raised when a database is required but was not set."""  # <1>


class DbRecord(Record):  # <2>

    __db = None  # <3>

    @staticmethod  # <4>
    def set_db(db):
        DbRecord.__db = db  # <5>

    @staticmethod  # <6>
    def get_db():
        return DbRecord.__db

    @classmethod  # <7>
    def fetch(cls, ident):
        db = cls.get_db()
        try:
            return db[ident]  # <8>
        except TypeError:
            if db is None:  # <9>
                msg = "database not set; call '{}.set_db(my_db)'"
                raise MissingDatabaseError(msg.format(cls.__name__))
            else:  # <10>
                raise

    def __repr__(self):
        if hasattr(self, 'serial'):  # <11>
            cls_name = self.__class__.__name__
            return '<{} serial={!r}>'.format(cls_name, self.serial)
        else:
            return super().__repr__()  # <12>


class Event(DbRecord):  # <1>

    @property
    def venue(self):
        key = 'venue.{}'.format(self.venue_serial)
        return self.__class__.fetch(key)  # <2>

    @property
    def speakers(self):
        if not hasattr(self, '_speaker_objs'):  # <3>
            speakers_serials = self.__dict__['speakers']  # <4>
            fetch = self.__class__.fetch  # <5>
            self._speaker_objs = [fetch('speaker.{}'.format(key))
                                  for key in speakers_serials]  # <6>
        return self._speaker_objs  # <7>

    def __repr__(self):
        if hasattr(self, 'name'):  # <8>
            cls_name = self.__class__.__name__
            return '<{} {!r}>'.format(cls_name, self.name)
        else:
            return super().__repr__()  # <9>


def load_db(db):
    raw_data = osconfeed.load()
    warnings.warn('loading ' + DB_NAME)
    for collection, rec_list in raw_data['Schedule'].items():
        record_type = collection[:-1]  # <1>
        cls_name = record_type.capitalize()  # <2>
        cls = globals().get(cls_name, DbRecord)  # <3>
        if inspect.isclass(cls) and issubclass(cls, DbRecord):  # <4>
            factory = cls  # <5>
        else:
            factory = DbRecord  # <6>
        for record in rec_list:  # <7>
            key = '{}.{}'.format(record_type, record['serial'])
            record['serial'] = key
            db[key] = factory(**record)  # <8>
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

- `__class__`

  对象属性的引用（即`obj.__class__`与`type(obj)`的作用相同）。Python 的某些特殊方法，例如`__getattr__`，只在对象的类中寻找，而不在实例中寻找

- `__dict__`

  一个映射，存储对象或类的可写属性。有`__dict__`属性的对象，任何时候都能随意设置新属性。如果类有`__slots__`属性，它的实例可能没有`__dict__`属性。

- `__slots__`

  类可以定义这个属性，限制实例能有哪些属性。`__slots__`属性的值是一个字符串组成的元组，指明允许有的属性。如果`__slots__`中没有`__dict__`，那么该类的实例没有`__dict__`属性，实例只允许有指定名称的属性。

- `dir([object])`

  如果没有实参，则返回当前本地作用域中的名称列表。如果有实参，它会尝试返回该对象的有效属性列表。参考[官方文档说明](https://docs.python.org/zh-cn/3/library/functions.html#dir)

- `vars([object])`

  返回模块、类、实例或任何其它具有`__dict__`属性的对象的`__dict__`属性。参考[官方文档说明](https://docs.python.org/zh-cn/3/library/functions.html#vars)

- `__getattribute__`

  此方法会无条件地被调用以实现对类实例属性的访问。如果类还定义了`__getattr__()`，则后者不会被调用，除非`__getattribute__()`显式地调用它或是引发了`AttributeError`。[官方文档说明](https://docs.python.org/zh-cn/3/reference/datamodel.html#object.__getattribute__)

>对于自定义类来说，如果隐式调用特殊方法，仅当特殊方法在对象所属的类型上定义，而不是在对象的实例字典中定义时，才能确保调用成功。

Last Modified 2022-08-07
