# 数据类型

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

# tag::SPADES_HIGH[]
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

# end::SPADES_HIGH[]


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

# tag::COORDINATE[]
from typing import NamedTuple

class Coordinate(NamedTuple):
    lat: float                # <1>
    lon: float
    reference: str = 'WGS84'  # <2>
# end::COORDINATE[]
```

```python
"""
match_cities.py
"""

# tag::CITY[]
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


# end::CITY[]

# tag::ASIA[]
def match_asian_cities():
    results = []
    for city in cities:
        match city:
            case City(continent='Asia'):
                results.append(city)
    return results


# end::ASIA[]

# tag::ASIA_POSITIONAL[]
def match_asian_cities_pos():
    results = []
    for city in cities:
        match city:
            case City('Asia'):
                results.append(city)
    return results


# end::ASIA_POSITIONAL[]


# tag::ASIA_COUNTRIES[]
def match_asian_countries():
    results = []
    for city in cities:
        match city:
            case City(continent='Asia', country=cc):
                results.append(cc)
    return results


# end::ASIA_COUNTRIES[]

# tag::ASIA_COUNTRIES_POSITIONAL[]
def match_asian_countries_pos():
    results = []
    for city in cities:
        match city:
            case City('Asia', _, country):
                results.append(country)
    return results


# end::ASIA_COUNTRIES_POSITIONAL[]


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

# tag::COORDINATE[]

from dataclasses import dataclass

# 默认情况下示例是可变的，除非指定 frozen=True
@dataclass(frozen=True)
class Coordinate:
    lat: float
    lon: float

    def __str__(self):
        ns = 'N' if self.lat >= 0 else 'S'
        we = 'E' if self.lon >= 0 else 'W'
        return f'{abs(self.lat):.1f}°{ns}, {abs(self.lon):.1f}°{we}'
# end::COORDINATE[]
```

Last Modified 2023-05-27
