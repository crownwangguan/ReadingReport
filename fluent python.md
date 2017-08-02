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


Slicing:

* the length of a slice or range, the stop position is given: range(3) and my_list[:3]
* the lenght of a slice or range, the start and stop are given: stop - start
* without overlapping:
```Python
  l=[10,20,30,40,50,60]
  l[:2] # split at 2
  >>> [10, 20]
  l[2:] # from 2
  >>> [30, 40, 50, 60]
```

[a::b]
from a, each step b
```Python
deck[12::13]
>>> [Card(rank='A', suit='spades'), Card(rank='A', suit='diamonds'), Card(rank='A', suit='clubs'), Card(rank='A', suit='hearts')]
```

``seq[start:stop:step]`` python actually call ``seq.__getitem__(slice(start, stop, step))``


When the target of the assignment is a slice, the right-hand side must be an iterable object, even if it has just one item.
```python
l[2:5] = 100 --wrong
l[2:5] = [100]
```

``+`` & ``*``

Usually both operands of ``+`` must be of the same sequence type, and neither of them is modified but a new sequence of the same type is created as result of the concatenation.
```python
l=[1,2,3]
l*5
>>>[1, 2, 3, 1, 2, 3, 1, 2, 3, 1, 2, 3, 1, 2, 3]
```
Both ``+`` and ``*`` always create a new object, and never change their operands.

Augment assignment:

The special method that makes ``+=`` work is ``__iadd__`` (for “in-place addition”). However, if ``__iadd__`` is not implemented, Python falls back to calling ``__add__``.

```python
t=(1,2,[30,40])
t[2] += [50, 60]
Traceback (most recent call last):
File "<stdin>", line 1, in <module>
TypeError: 'tuple' object does not support item assignment

>>> t
(1, 2, [30, 40, 50, 60])
```

[PythonTutor](http://www.pythontutor.com)

* Putting mutable items in tuples is not a good idea.
* Augmented assignment is not an atomic operation.
* Inspecting Python bytecode is not too difficult, and is often helpful to see what is going on under the hood.


``list.sort`` and ``sorted`` built-in function

The ``list.sort`` method sorts a list in-place, that is, without making a copy. It returns None to remind us that it changes the target object, and does not create a new list. Same as random.shuffle function.

In contrast, the built-in function ``sorted`` creates a new list and returns it. In fact, sorted accepts any iterable object as argument, including immutable sequences and generators. Regardless of the type of iterable given to sorted, it always returns a newly created list.

Both ``list.sort`` and ``sorted`` take two optional, keyword-only arguments: key and reverse.
* reverse
* key
