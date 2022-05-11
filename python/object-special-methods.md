# 对象特殊方法

```python
import random
import collections

Card = collections.namedtuple('Card', ['rank', 'suit'])


class FrenchDeck:
    ranks = [str(n) for n in range(2, 11)] + list('JQKA')
    suits = 'spades diamonds clubs heart'.split()
    suits_values = dict(spades=3, heart=2, diamonds=1, clubs=0)

    def __init__(self):
        self._cards = [Card(rank, suit) for suit in self.suits for rank in self.ranks]

    def __len__(self):
        return len(self._cards)

    def __getitem__(self, key):
        return self._cards[key]

    def spades_high(self, card):
        rank_value = self.ranks.index(card.rank)
        return rank_value * len(self.suits_values) + self.suits_values[card.suit]


if __name__ == '__main__':
    deck = FrenchDeck()
    print(random.choice(deck))
    sorted_deck = sorted(deck, key=deck.spades_high)
    print(sorted_deck[:10])
```

```python
import math


class Vector:

    def __init__(self, x=0, y=0):
        self.x = x
        self.y = y

    def __repr__(self):
        return f'Vector({self.x}, {self.y})'

    def __abs__(self):
        return math.hypot(self.x, self.y)

    def __bool__(self):
        # return bool(abs(self))
        return bool(self.x or self.y)

    def __add__(self, other):
        x = self.x + other.x
        y = self.y + other.y
        return Vector(x, y)

    def __mul__(self, scalar):
        return Vector(self.x * scalar, self.y * scalar)


if __name__ == '__main__':
    v1 = Vector(3, 4)
    v2 = Vector(7, 9)
    print(v1 + v2)
    print(v1 * 3)
    print(abs(v1))
```

Last Modified 2022-05-11