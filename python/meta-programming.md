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

Last Modified 2022-08-07
