# 数据类型

面向对象编程的主要思想是把行为和数据放在同一个代码单元（一个类）中。如果一个类使用广泛，但是自身没有什么重要的行为，那么整个系统中可能遍布处理实例的代码，并出现在很多方法和函数中（有些甚至是重复的）。这样的系统对维护来说简直就是噩梦。

## 具名元组

```python
"""
>>> beer_card = Card('7', 'diamonds')
>>> beer_card
Card(rank='7', suit='diamonds')
>>> deck = FrenchDeck()
>>> len(deck)
52
>>> deck[:3]
[Card(rank='2', suit='spades'), Card(rank='3', suit='spades'), Card(rank='4', suit='spades')]
>>> deck[12::13]
[Card(rank='A', suit='spades'), Card(rank='A', suit='diamonds'), Card(rank='A', suit='clubs'), Card(rank='A', suit='hearts')]
>>> Card('Q', 'hearts') in deck
True
>>> Card('Z', 'clubs') in deck
False
>>> for card in deck:  # doctest: +ELLIPSIS
...   print(card)
Card(rank='2', suit='spades')
Card(rank='3', suit='spades')
Card(rank='4', suit='spades')
...
>>> for card in reversed(deck):  # doctest: +ELLIPSIS
...   print(card)
Card(rank='A', suit='hearts')
Card(rank='K', suit='hearts')
Card(rank='Q', suit='hearts')
...
>>> for n, card in enumerate(deck, 1):  # doctest: +ELLIPSIS
...   print(n, card)
1 Card(rank='2', suit='spades')
2 Card(rank='3', suit='spades')
3 Card(rank='4', suit='spades')
...

Sort with *spades high* overall ranking

>>> Card.suit_values = dict(spades=3, hearts=2, diamonds=1, clubs=0)  # <1>
>>> def spades_high(card):                                            # <2>
...     rank_value = FrenchDeck.ranks.index(card.rank)
...     suit_value = card.suit_values[card.suit]
...     return rank_value * len(card.suit_values) + suit_value
...
>>> Card.overall_rank = spades_high                                   # <3>
>>> lowest_card = Card('2', 'clubs')
>>> highest_card = Card('A', 'spades')
>>> lowest_card.overall_rank()                                        # <4>
0
>>> highest_card.overall_rank()
51



>>> for card in sorted(deck, key=Card.overall_rank):  # doctest: +ELLIPSIS
...      print(card)
Card(rank='2', suit='clubs')
Card(rank='2', suit='diamonds')
Card(rank='2', suit='hearts')
...
Card(rank='A', suit='diamonds')
Card(rank='A', suit='hearts')
Card(rank='A', suit='spades')
"""
import collections

Card = collections.namedtuple('Card', ['rank', 'suit'])


class FrenchDeck:
    ranks = [str(n) for n in range(2, 11)] + list('JQKA')
    suits = 'spades diamonds clubs hearts'.split()

    def __init__(self):
        self._cards = [Card(rank, suit) for suit in self.suits
                       for rank in self.ranks]

    def __len__(self):
        return len(self._cards)

    def __getitem__(self, position):
        return self._cards[position]
```

## 带类型具名元组

```python
"""
``Coordinate``: a simple ``NamedTuple`` subclass

This version has a field with a default value::

    >>> moscow = Coordinate(55.756, 37.617)
    >>> moscow
    Coordinate(lat=55.756, lon=37.617, reference='WGS84')

"""

from typing import NamedTuple

class Coordinate(NamedTuple):
    lat: float                # <1>
    lon: float
    reference: str = 'WGS84'  # <2>
```

```python
"""
match_cities.py
"""

import typing


class City(typing.NamedTuple):
    continent: str
    name: str
    country: str


cities = [
    City('Asia', 'Tokyo', 'JP'),
    City('Asia', 'Delhi', 'IN'),
    City('North America', 'Mexico City', 'MX'),
    City('North America', 'New York', 'US'),
    City('South America', 'São Paulo', 'BR'),
]



def match_asian_cities():
    results = []
    for city in cities:
        match city:
            case City(continent='Asia'):
                results.append(city)
    return results



def match_asian_cities_pos():
    results = []
    for city in cities:
        match city:
            case City('Asia'):
                results.append(city)
    return results




def match_asian_countries():
    results = []
    for city in cities:
        match city:
            case City(continent='Asia', country=cc):
                results.append(cc)
    return results



def match_asian_countries_pos():
    results = []
    for city in cities:
        match city:
            case City('Asia', _, country):
                results.append(country)
    return results




def match_india():
    results = []
    for city in cities:
        match city:
            case City(_, name, 'IN'):
                results.append(name)
    return results


def match_brazil():
    results = []
    for city in cities:
        match city:
            case City(country='BR', name=name):
                results.append(name)
    return results


def main():
    tests = ((n, f) for n, f in globals().items() if n.startswith('match_'))

    for name, func in tests:
        print(f'{name:15}\t{func()}')


if __name__ == '__main__':
    main()
```

## 装饰类

```python
"""
``Coordinate``: simple class decorated with ``dataclass`` and a custom ``__str__``::

    >>> moscow = Coordinate(55.756, 37.617)
    >>> print(moscow)
    55.8°N, 37.6°E

"""


from dataclasses import dataclass, asdict, field


# 默认情况下示例是可变的，除非指定 frozen=True
@dataclass(frozen=True)
class Coordinate:
    lat: float
    lon: float

    def __str__(self):
        ns = 'N' if self.lat >= 0 else 'S'
        we = 'E' if self.lon >= 0 else 'W'
        return f'{abs(self.lat):.1f}°{ns}, {abs(self.lon):.1f}°{we}'



@dataclass
class Point:
    x: int
    y: int


@dataclass
class C:
    mylist: list[Point]


p = Point(10, 20)
assert asdict(p) == {'x': 10, 'y': 20}

c = C([Point(0, 0), Point(10, 4)])
assert asdict(c) == {'mylist': [{'x': 0, 'y': 0}, {'x': 10, 'y': 4}]}


# default_factory: 如果提供, 它必须是一个零参数可调用对象, 当该字段需要一个默认值时, 它将被调用
@dataclass
class D:
    mylist: list[int] = field(default_factory=list)


d = D()
d.mylist += [1, 2, 3]
print(d)
```

这个装饰器还支持其它的参数，参考官方说明 https://docs.python.org/zh-cn/3/library/dataclasses.html

```python
"""
``HackerClubMember`` objects accept an optional ``handle`` argument::

    >>> anna = HackerClubMember('Anna Ravenscroft', handle='AnnaRaven')
    >>> anna
    HackerClubMember(name='Anna Ravenscroft', guests=[], handle='AnnaRaven')

If ``handle`` is omitted, it's set to the first part of the member's name::

    >>> leo = HackerClubMember('Leo Rochael')
    >>> leo
    HackerClubMember(name='Leo Rochael', guests=[], handle='Leo')

Members must have a unique handle. The following ``leo2`` will not be created,
because its ``handle`` would be 'Leo', which was taken by ``leo``::

    >>> leo2 = HackerClubMember('Leo DaVinci')
    Traceback (most recent call last):
      ...
    ValueError: handle 'Leo' already exists.

To fix, ``leo2`` must be created with an explicit ``handle``::

    >>> leo2 = HackerClubMember('Leo DaVinci', handle='Neo')
    >>> leo2
    HackerClubMember(name='Leo DaVinci', guests=[], handle='Neo')
"""

from dataclasses import dataclass, field


@dataclass
class ClubMember:
    name: str
    guests: list[str] = field(default_factory=list)  # <1>


@dataclass
class HackerClubMember(ClubMember):  # <1>
    all_handles = set()  # <2>
    handle: str = ''  # <3>

    def __post_init__(self):
        cls = self.__class__  # <4>
        if self.handle == '':  # <5>
            self.handle = self.name.split()[0]
        if self.handle in cls.all_handles:  # <6>
            msg = f'handle {self.handle!r} already exists.'
            raise ValueError(msg)
        cls.all_handles.add(self.handle)  # <7>
```

## 都柏林核心模式

```python
"""
Media resource description class with subset of the Dublin Core fields.

Default field values:

    >>> r = Resource('0')
    >>> r  # doctest: +NORMALIZE_WHITESPACE
    Resource(
        identifier = '0',
        title = '<untitled>',
        creators = [],
        date = None,
        type = <ResourceType.BOOK: 1>,
        description = '',
        language = '',
        subjects = [],
    )

A complete resource record:

    >>> description = 'Improving the design of existing code'
    >>> book = Resource('978-0-13-475759-9', 'Refactoring, 2nd Edition',
    ...     ['Martin Fowler', 'Kent Beck'], date(2018, 11, 19),
    ...     ResourceType.BOOK, description, 'EN',
    ...     ['computer programming', 'OOP'])

    >>> book  # doctest: +NORMALIZE_WHITESPACE
    Resource(
        identifier = '978-0-13-475759-9',
        title = 'Refactoring, 2nd Edition',
        creators = ['Martin Fowler', 'Kent Beck'],
        date = datetime.date(2018, 11, 19),
        type = <ResourceType.BOOK: 1>,
        description = 'Improving the design of existing code',
        language = 'EN',
        subjects = ['computer programming', 'OOP'],
    )

"""

from dataclasses import dataclass, field, fields
from typing import Optional, TypedDict
from enum import Enum, auto
from datetime import date


class ResourceType(Enum):
    BOOK = auto()
    EBOOK = auto()
    VIDEO = auto()


@dataclass
class Resource:
    """Media resource description."""
    identifier: str
    title: str = '<untitled>'
    creators: list[str] = field(default_factory=list)
    date: Optional[date] = None
    type: ResourceType = ResourceType.BOOK
    description: str = ''
    language: str = ''
    subjects: list[str] = field(default_factory=list)

    def __repr__(self):
        cls = self.__class__
        cls_name = cls.__name__
        indent = ' ' * 4
        res = [f'{cls_name}(']                            # <1>
        for f in fields(cls):                             # <2>
            value = getattr(self, f.name)                 # <3>
            res.append(f'{indent}{f.name} = {value!r},')  # <4>

        res.append(')')                                   # <5>
        return '\n'.join(res)                             # <6>


class ResourceDict(TypedDict):
    identifier: str
    title: str
    creators: list[str]
    date: Optional[date]
    type: ResourceType
    description: str
    language: str
    subjects: list[str]


if __name__ == '__main__':
    r = Resource('0')
    description = 'Improving the design of existing code'
    book = Resource('978-0-13-475759-9', 'Refactoring, 2nd Edition',
                    ['Martin Fowler', 'Kent Beck'], date(2018, 11, 19),
                    ResourceType.BOOK, description,
                    'EN', ['computer programming', 'OOP'])
    print(book)
    book_dict: ResourceDict = {
        'identifier': '978-0-13-475759-9',
        'title': 'Refactoring, 2nd Edition',
        'creators': ['Martin Fowler', 'Kent Beck'],
        'date': date(2018, 11, 19),
        'type': ResourceType.BOOK,
        'description': 'Improving the design of existing code',
        'language': 'EN',
        'subjects': ['computer programming', 'OOP']}
    book2 = Resource(**book_dict)
    print(book == book2)
```

如果想要更加高级的功能，可以看看`attrs`项目，这个项目比`@dataclass`要早一些

Last Modified 2023-05-28
