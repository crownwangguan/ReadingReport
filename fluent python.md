# Reading notes for *Fluent Python*

## Week1

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

#### str  vs  repr

The ``__repr__`` special method is called by the repr built-in to get string representation of the object for inspection. If we did not implement ``__repr__``, vector instances would be shown in the console like ``<Vector object at 0x10e100070>``.

Contrast ``__repr__`` with ``__str__``, which is called by the str() constructor and implicitly used by the print function. ``__str__`` should return a string suitable for display to end-users.

[``__str__`` vs ``__repr__``](https://stackoverflow.com/questions/1436703/difference-between-str-and-repr-in-python)


Python array of sequences

* ** container sequences **:
list, tuple and collections.deque can hold items of different types.
* ** flat sequences **:
str, bytes, bytearray, memoryview and array.array hold items of one type. Flat sequences are more compact, but they are limited to holding primitive values like characters, bytes and numbers

* ** mutable sequences **:
list, bytearray, array.array, collections.deque and memoryview

* ** immutable sequences **:
tuple, str and bytes

__*List Comprehension:*__
only do one thing, build a new list
```Python
symbols = '$¢£¥€¤'
codes = [ord(symbol) for symbol in symbols]
```

line breaks are ignored inside pairs of [], {}, ()

List Comprehension do all things map and filter do:
```Python
symbols = '$¢£¥€¤'
beyond_ascii = [ord(s) for s in symbols if ord(s) > 127]
```
equals to:
```Python
beyond_ascii = list(filter(lambda c: c > 127, map(ord, symbols)))
```

cartesian products:
```Python
tshirts = [(color, size) for color in colors for size in sizes]

tshirts = [(color, size) for size in sizes
                        for color in colors]
```

__Generator:__

genexp saves memory because it yields items one by one using the iterator protocol instead of building a whole list just to feed another constructor

Genexps use the same syntax as listcomps, but are enclosed in parenthesis rather than brackets.
```Python
symbols = '$¢£¥€¤'
tuple(ord(symbol) for symbol in symbols)
```

__Tuple:__

``_`` used as place holder

``*`` to grab excess items

```Python
a, b, *rest = range(5)
rest = [2, 3, 4]
```

nested tuple unpacking:
```Python
metro_areas = [ ('Tokyo','JP',36.933,(35.689722,139.691667)),
('Delhi NCR', 'IN', 21.935, (28.613889, 77.208889)),
('Mexico City', 'MX', 20.142, (19.433333, -99.133333)),
('New York-Newark', 'US', 20.104, (40.808611, -74.020386)),
('Sao Paulo', 'BR', 19.649, (-23.547778, -46.635833)),]

print('{:15} | {:^9} | {:^9}'.format('', 'lat.', 'long.'))
fmt = '{:15} | {:9.4f} | {:9.4f}'

for name, cc, pop, (latitude, longitude) in metro_areas:
  if longitude <= 0:
    print(fmt.format(name, latitude, longitude))
```

Named Tuple:

The collections.namedtuple function is a factory that produces subclasses of tuple enhanced with field names and a class name.


Instances of a class that you build with namedtuple take exactly the same amount of memory as tuples because the field names are stored in the class. They use less memory than a regular object because they do store attributes in a per-instance ``__dict__``.

```Python
from collections import namedtuple

City = namedtuple('City', 'name country population coordinates')
tokyo = City('Tokyo', 'JP', 36.933, (35.689722, 139.691667))

>>> tokyo
City(name='Tokyo', country='JP', population=36.933, coordinates=(35.689722, 139.691667))
```

* ``_fields`` is a tuple with the field names of the class.
* ``_make()`` lets you instantiate a named tuple from an iterable; City(*delhi_data) would do the same.
* ``_asdict()`` returns a ``collections.OrderedDict`` built from the named tuple instance.

all list methods that do not involve adding or removing items are supported by tuple with one exception — tuple lacks the ``__reversed__`` method, but that is just for optimization; ``reversed(my_tuple)`` works without it.
