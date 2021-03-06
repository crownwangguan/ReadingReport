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

[generator vs list comprehensions](https://www.codementor.io/djangostars/python-list-comprehensions-generator-expressions-6dnzuh12c)

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


__bisect__

``bisect(haystack, needle)`` does a ``binary search`` for ``needle`` in ``haystack`` — which must be a ``sorted sequence`` — to locate the position where needle can be inserted while maintaining haystack in ``ascending order``. In other words, all items appearing up to that position are less or equal to needle.

```Python
def grade(score, breakpoints=[60, 70, 80, 90], grades='FDCBA'):
  i = bisect.bisect(breakpoints, score)
  return grades[i]

[grade(score) for score in [33, 99, 77, 70, 89, 90, 100]]
>>> ['F', 'A', 'C', 'C', 'B', 'A', 'A']
```

``insort(seq, item)`` inserts ``item`` into ``seq`` so as to keep seq in ascending order.

```Python
import bisect
import random

SIZE=7
random.seed(1729)
my_list = []

for i in range(SIZE):
    new_item = random.randrange(SIZE*2)
    bisect.insort(my_list, new_item)
    print('%2d ->' % new_item, my_list)
```

__Recommand__

*  if need to store 10 million of floating point values an ``array`` is much more efficient, because an array does not actually hold full-fledged float objects, but only the packed bytes representing their machine values

*  if constantly adding and removing items from the ends of a list as a FIFO or LIFO data structure, a ``deque`` (double-ended queue) works faster.

* If code does a lot of containment checks — e.g. ``item in my_col lection``, consider using a ``set`` for my_collection, especially if it holds a large number of items. Sets are optimized for fast membership checking. But they are not sequences

##### Array:
To list of numbers, use ``array.array``. Supports all mutable sequence operations (including ``.pop``, ``.insert`` and ``.extend``), and additional methods for fast loading and saving such as ``.frombytes`` and ``.tofile``.

__Numpy:__
NumPy implements multi-dimensional, homogeneous arrays and matrix types that hold not only numbers but also user defined records, and provides efficient elementwise operations.

__Scipy:__
SciPy is a library, written on top of NumPy, offering many scientific computing algorithms from linear algebra, numerical calculus and statistics.


```Python
>>> import numpy
>>> a = numpy.arange(12)
>>> a
array([ 0, 1, 2, 3, 4, 5, 6, >>> type(a)
<class 'numpy.ndarray'>
>>> a.shape
(12,)
>>> a.shape = 3, 4
>>> a
array([[ 0, 1, 2, 3],
[ 4, 5, 6, 7],
[ 8, 9, 10, 11]]) >>> a[2]
array([ 8,  9, 10, 11])
>>> a[2, 1]
9
>>> a[:, 1] array([1, 5, 9])
>>> a.transpose() array([[ 0, 4, 8],
       [ 1,  5,  9],
       [ 2,  6, 10],
       [ 3,  7, 11]])
```

__Deque:__

The ``.append`` and ``.pop`` methods make a list usable as a stack or a queue (if you use .append and .pop(0), you get LIFO behavior). But inserting and removing from the left of a list (the 0-index end) is costly because the entire list must be shifted.

The class ``collections.deque`` is a thread-safe double-ended queue designed for fast inserting and removing from both ends.

```Python
from collections import deque
dq = deque(range(10), maxlen=10)
dq.rotate(3)
>>> deque([7, 8, 9, 0, 1, 2, 3, 4, 5, 6], maxlen=10)
dq.rotate(-4)
>>> deque([1, 2, 3, 4, 5, 6, 7, 8, 9, 0], maxlen=10)
dq.appendleft(-1)
>>> deque([-1, 1, 2, 3, 4, 5, 6, 7, 8, 9], maxlen=10)
dq.extend([11, 22, 33])
>>> deque([3, 4, 5, 6, 7, 8, 9, 11, 22, 33], maxlen=10)
dq.extendleft([10, 20, 30, 40])
>>> deque([40, 30, 20, 10, 3, 4, 5, 6, 7, 8], maxlen=10)
```

*The append and popleft operations are atomic, so deque is safe to use as a LIFO-queue in multi-threaded applications without the need for using locks.*



### Dict

The built-in functions live in`` __builtins__.__dict__``.
``Hash tables`` are the engines behind Python’s high performance dicts.

python 3
``collections.abc`` provides abstract base classes that can be used to test whether a class provides a particular interface; for example, whether it is hashable or whether it is a mapping.

All mapping types in the standard library use the basic ``dict`` in their implementation, so they share the limitation that the ``keys`` must be ``hashable`` (the values need not be hashable, only the keys).

*What is Hashable*

An object is ``hashable`` if it has a ``hash`` value which never changes during its lifetime (it needs a ``__hash__()`` method), and can be compared to other objects (it needs an ``__eq__()`` method). Hashable objects which compare equal must have the same hash value.

The atomic immutable types (``str``, ``bytes``, ``numeric types``) are all hashable. A ``frozen set`` is always hashable, because its elements must be hashable by definition. A ``tuple`` is hashable only if all its items are hashable.

User-defined types are hashable by default because their hash value is their ``id()`` and they all compare not equal. If an object implements a custom ``__eq__`` that takes into account its internal state, it may be hashable only if all its attributes are immutable.

How to build dict:
```python
>>> a = dict(one=1, two=2, three=3)
>>> b = {'one': 1, 'two': 2, 'three': 3}
>>> c = dict(zip(['one', 'two', 'three'], [1, 2, 3]))
>>> d = dict([('two', 2), ('one', 1), ('three', 3)])
>>> e = dict({'three': 3, 'one': 1, 'two': 2})
```


## Week2

dict comprehensions
```python
DIAL_CODES = [
  (86, 'China'),
  (91, 'India'),
  (1, 'United States'),
  (62, 'Indonesia'),
  (55, 'Brazil'),
  (92, 'Pakistan'),
  (880, 'Bangladesh'),
  (234, 'Nigeria'),
  (7, 'Russia'),
  (81, 'Japan'),
]

country_code = {country: code for code, country in DIAL_CODES}
```

Handle missing key with setdefault:
``index.setdefault(word, []).append(location).`` (Get the list of occurrences for word, or set it to [] if not found; setdefault returns the value, so it can be updated without requiring a second search.)


Mapping with flexible key lookup:

* defaultdict:

  For example, given an empty defaultdict created as dd = defaultdict(list), if 'new-key' is not in dd then the expression dd['new-key'] does the following steps:
  1. calls list() to create a new list;
  2. inserts the list into dd using 'new-key' as key;
  3. returns a reference to that list.

* subclassing userdict:

  Note that UserDict does not inherit from dict, but has an internal dict instance, called data, which holds the actual items.

  ```Python
  import collections

  class StrKeyDict(collections.UserDict):

    def __missing__(self, key):
      if isinstance(key, str):
        raise KeyError(key) return self[str(key)]

    def __contains__(self, key):
      return str(key) in self.data

    def __setitem__(self, key, item):
      self.data[str(key)] = item
  ```

  [dict vs userdict](https://stackoverflow.com/questions/7148419/subclass-dict-userdict-dict-or-abc)


immutable mapping

The mapping types provided by the standard library are all mutable, but you may need to guarantee that a user cannot change a mapping by mistake. Updates to the original mapping can be seen in the mappingproxy, but changes cannot be made through it.

```Python
>>> from types import MappingProxyType >>>d={1:'A'}
>>> d_proxy = MappingProxyType(d)
>>> d_proxy
mappingproxy({1: 'A'})
>>> d_proxy[1]
'A'
>>> d_proxy[2] = 'x'
Traceback (most recent call last):
File "<stdin>", line 1, in <module>
TypeError: 'mappingproxy' object does not support item assignment >>> d[2] = 'B'
>>> d_proxy
mappingproxy({1: 'A', 2: 'B'})
```

Set:

A set is a collection of unique objects.
```Python
>>>l = ['spam', 'spam', 'eggs', 'spam']
>>>set(l)
>>>{'eggs', 'spam'}
```
Set elements must be hashable. The ``set`` type is not hashable, but ``frozenset`` is

Given two sets a and b, ``a | b`` returns their union, ``a & b`` computes the intersection, and ``a - b`` the difference.
```Python
found = len(set(needles) & set(haystack))

vs

found = 0
for n in needles:
  if n in haystack:
    found += 1
```

Set literals:

quicker than set(list)

Don’t forget: to create an ``empty set``, use the constructor without an argument: ``set()``. If you write ``{}``, you’re creating an empty ``dict`` — this hasn’t changed.

```Python
>>> s={1, 2, 3}
>>> type(s) <class 'set'>
>>> s
{1, 2, 3}
```

check bytecode for {1} and set([1])

```Python
>>> from dis import dis
>>> dis('{1}')
      1           0 LOAD_CONST
                  3 BUILD_SET
                  6 RETURN_VALUE
>>> dis('set([1])')
      1           0 LOAD_NAME
                  3 LOAD_CONST
                  6 BUILD_LIST
                  9 CALL_FUNCTION
                 12 RETURN_VALUE
```


Set comprehensions:

```Python
>>> from unicodedata import name
>>> {chr(i) for i in range(32, 256) if 'SIGN' in name(chr(i),'')}
{'§', '=', '¢', '#', '¤', '<', '¥', 'μ', '×', '$', '¶', '£', '©', '°', '+', '÷', '±', '>', '¬', '®', '%'}
```

Dict and Set in python use hashtable:

A hash table is a sparse array, i.e. an array which always has empty cells. In standard data structure texts, the cells in a hash table are often called “buckets”. In a dict hash table, there is a bucket for each item, and it contains two fields: a reference to the key and a reference to the value of the item. Because all buckets have the same size, access to an individual bucket is done by offset.

Python tries to keep at least 1/3 of the buckets empty; if the hash table becomes too crowded, it is copied to a new location with room for more buckets.

To put an item in a hash table the first step is to calculate the hash value of the item key, which is done with the hash() built-in function


Hashes and equality

The ``hash()`` built-in function works directly with built-in types and falls back to calling ``__hash__`` for user-defined types. If two objects compare equal, their hash values must also be equal, otherwise the hash table algorithm does not work.


The hash table algorithm

To fetch the value at ``my_dict[search_key]``, Python calls ``hash(search_key)`` to obtain the hash value of search_key and uses the least significant bits of that number as an offset to look up a bucket in the hash table (the number of bits used depends on the current size of the table). If the found bucket is empty, KeyError is raised. Otherwise, the found bucket has an item — a ``found_key:found_value`` pair — and then Python checks whether ``search_key == found_key``. If they match, that was the item sought: ``found_value`` is returned.

However, if search_key and found_key do not match, this is a ``hash collision``. This happens because a hash function maps arbitrary objects to a small number of bits, and — in addition — the hash table is indexed with a subset of those bits. To resolve the collision, the algorithm then takes different bits in the hash, massages them in a particular way and uses the result as an offset to look up a different bucket8. If that is empty,    ``KeyError is raised``; if not, either the keys match and the item value is returned, or the collision resolution process is repeated.

dict:

* Keys must be hashable objects
* dicts have significant memory overhead
* Key search is very fast
* Key ordering depends on insertion order
* Adding items to a dict may change the order of existing keys



## Decorator:
[Decorator introduction](https://www.thecodeship.com/patterns/guide-to-python-function-decorators/)

[How Flask use decorator to demonstrate](http://flask.pocoo.org/snippets/129/)


Encode and decode:

bytes -> string (read content from text (decode))
string -> bytes (write to text (encode))


## Function:

modern replacements for map, filter and reduce

with the list comprehensions and generator expression:
  (Python3 map and filter return generators)
```python
list(map(fact, range(6)))
==>
[fact(n) for n in range(6)]
----------------------------------------
list(map(factorial, filter(lambda n: n % 2, range(6))))
==>
[factorial(n) for n in range(6) if n % 2]
----------------------------------------
from functools import reduce
from operator import add

reduce(add, range(100))
==>
sum(range(100))
```

** Lambda **:
```python
fruits = ['a', 'b', 'c', 'd']
sorted(fruits, key=lambda word: word[::-1])
```

Callable objects:

* user-defined function:
created with ``def`` statements or ``lambda`` expressions
* built-in function:
a function implemented in C, like ``len`` or ``time.strftime``
* built-in methods:
methods implemented in C, like ``dict.get``
* methods:
functions defined in the body of a class
* classes:
when invoded, a class runs its ``__new__`` to create an instance, then ``__init__`` to initialize
* instances:
if a class defines a ``__call__`` method, instance maybe invoked as functions
* generator function:
``yield`` keyword, generator function return a generator object

User defined callable types:

python objects behave like functions:
```python
import random

class BingoCage:
  def __init__(self, items):
    self._items = list(items)
    random.shuffle(self._items)

  def pick(self):
    try:
      return self._items.pop()
    except IndexError:
      raise LookupError('pick from empty cage')

  def __call__(self):
    return self.pick()


bingo = BingoCage(range(3))
bingo.pick()
>> 1
bingo()
>> 0
```


Positional to keyword-only parameters:

``*`` and ``**`` to explode iterables and mappings into separate arguments

```python
def tag(name, *content, cls=None, **attrs):
  if cls is not None:
    attrs['class'] = cls
  if attrs:
    attr_str = ''.join(' %s="%s"' % (attr, value) for attr, value in sorted(attrs.items()))
  else:
    attr_str = ''
  if content:
    return '\n'.join('<%s%s>%s</%s>' % (name, attr_str, c, name) for c in content)
  else:
    return '<%s%s /> % (name, attr_str)
```

** Annotation **:

function annotation used to set pre-condition or validation for arguments.

[Annotation useful](goo.gl/J5WYiW)


** Functional programming: **

``operator`` and ``functools`` module

```Python
from functools import reduce

def fact(n):
  return reduce(lambda a, b: a*b, range(1, n+1))

==>
from functools import reduce
from operator import mul

def fact(n):
  return reduce(mul, range(1, n+1))
```

``itemgetter`` & ``attrgetter`` & ``methodcaller``

itemgetter: common useage, soring a list of tuples by the value of one field
``itemgetter(1)`` does the same as ``lambda fields: fields[1]``
```Python
metro_data = [
('Tokyo', 'JP', 36.933, (35.689722, 139.691667)),
('Delhi NCR', 'IN', 21.935, (28.613889, 77.208889)),
('Mexico City', 'MX', 20.142, (19.433333, -99.133333)),
('New York-Newark', 'US', 20.104, (40.808611, -74.020386)),
('Sao Paulo', 'BR', 19.649, (-23.547778, -46.635833)), ... ]

>>> from operator import itemgetter
>>> for city in sorted(metro_data, key=itemgetter(1)):
      print(city)
```

attrgetter: create functions to extract object attributes by name. pass several attribute names as arguments, it also returns a tuple of values

```Python
from operator import attrgetter

name_lat = attrgetter('name', 'coord.lat')

for city in sorted(metro_areas, key=attrgetter('coord.lat')):
  print(name_lat(city))
```

methodcaller: creates calls a method by name on the object given as argument

```python
from operator import methodcaller

s = 'The time has come'
upcase = methodcaller('upper')
upcase(s)
```

** functools.partial **

[partial basic](https://www.pydanny.com/python-partials-are-fun.html)
