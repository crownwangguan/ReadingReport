# Reading notes for *Fluent Python*
---
## ** Week1 **

Python Data Model

```Python
import collections

Card = collections.namedtuple('Card', ['rank', 'suit'])

class FrenchDeck:

  ranks = [str(n) for n in range(2, 11)] + list('JQKA')
  suits = 'spades diamonds clubs hearts'.split()

  def __init__(self):
    self._cards = [Card(rank, suit) for suit in self.suits

  def __len__(self):
    return len(self._cards)

  def __getitem__(self, position):
    return self._cards[position]
```

After overwrite the function, we can do these:

``__init__``
> beer_card = Card('7', 'diamonds')

``__len__``
> len(deck)

``__getitem__``
> deck[0]

#### with the help of ``__getitem__`` it become iterable.
```Python
for card in deck:
  print(card)

OR

Card('Q', 'hearts') in deck
```

#### str * vs * repr

The ``__repr__`` special method is called by the repr built-in to get string representation of the object for inspection. If we did not implement ``__repr__``, vector instances would be shown in the console like ``<Vector object at 0x10e100070>``.

Contrast ``__repr__`` with ``__str__``, which is called by the str() constructor and implicitly used by the print function. ``__str__`` should return a string suitable for display to end-users.

[``__str__`` vs ``__repr__``](https://stackoverflow.com/questions/1436703/difference-between-str-and-repr-in-python)
