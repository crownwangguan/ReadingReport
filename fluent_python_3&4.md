## Week3

Design Patterns:

** Abstract method **

```python
from abc import ABC, abstractmethod
from collections import namedtuple

Customer = namedtuple('Customer', 'name fidelity')

class LineItem:
  def __init__(self, product, quantity, price): self.product = product
    self.quantity = quantity
    self.price = price
  def total(self):
    return self.price * self.quantity

class Order: # the Context
  def __init__(self, customer, cart, promotion=None):
    self.customer = customer
    self.cart = list(cart)
    self.promotion = promotion
  def total(self):
    if not hasattr(self, '__total'):
    self.__total = sum(item.total() for item in self.cart)
    return self.__total
  def due(self):
    if self.promotion is None:
      discount = 0
    else:
      discount = self.promotion.discount(self)
    return self.total() - discount
  def __repr__(self):
    fmt = '<Order total: {:.2f} due: {:.2f}>'
    return fmt.format(self.total(), self.due())

class Promotion(ABC): # the Strategy: an Abstract Base Class
    @abstractmethod
    def discount(self, order):
      """Return discount as a positive dollar amount"""

class FidelityPromo(Promotion): # first Concrete Strategy
  """5% discount for customers with 1000 or more fidelity points"""
  def discount(self, order):
    return order.total() * .05 if order.customer.fidelity >= 1000 else 0

class BulkItemPromo(Promotion): # second Concrete Strategy
  """10% discount for each LineItem with 20 or more units"""
  def discount(self, order):
    discount = 0
    for item in order.cart:
      if item.quantity >= 20:
        discount += item.total() * .1
    return discount

class LargeOrderPromo(Promotion): # third Concrete Strategy
  """7% discount for orders with 10 or more distinct items"""
  def discount(self, order):
  distinct_items = {item.product for item in order.cart}
  if len(distinct_items) >= 10:
    return order.total() * .07
  return 0
```

** Replacing the concrete strategies with simple functions, and removing the Promo abstract class. **

```python
from collections import namedtuple

Customer = namedtuple('Customer', 'name fidelity')

class LineItem:
  def __init__(self, product, quantity, price):
    self.product = product
    self.quantity = quantity
    self.price = price
  def total(self):
    return self.price * self.quantity

class Order: # the Context
  def __init__(self, customer, cart, promotion=None):
    self.customer = customer
    self.cart = list(cart)
    self.promotion = promotion
  def total(self):
    if not hasattr(self, '__total'):
      self.__total = sum(item.total() for item in self.cart)
      return self.__total
  def due(self):
    if self.promotion is None:
      discount = 0
    else:
      discount = self.promotion(self)
    return self.total() - discount
  def __repr__(self):
    fmt = '<Order total: {:.2f} due: {:.2f}>'
    return fmt.format(self.total(), self.due())

def fidelity_promo(order):
  """5% discount for customers with 1000 or more fidelity points"""
  return order.total() * .05 if order.customer.fidelity >= 1000 else 0

def bulk_item_promo(order):
"""10% discount for each LineItem with 20 or more units"""
  discount = 0
  for item in order.cart:
    if item.quantity >= 20:
      discount += item.total() * .1
  return discount

def large_order_promo(order):
"""7% discount for orders with 10 or more distinct items"""
  distinct_items = {item.product for item in order.cart}
  if len(distinct_items) >= 10:
    return order.total() * .07
  return 0

```
extract those functions to file and iterate
```python
promos = [fidelity_promo, bulk_item_promo, large_order_promo]
def best_promo(order):
  """Select best discount available"""
  return max(promo(order) for promo in promos)
```

### Decorator:
A decorator is a callable that takes another function as argument. The decorator may perform some processing with the decorated function, and returns it or replaces it with another function or callable object.

```python
@decorate
def target():
  print('running target()')

==> same
def target():
  print('running target()')

target = decorate(target)
```
_A key feature of decorators is that they run right after the decorated function is defined. That is usually at import time, i.e. when a module is loaded by Python._

** use decorator to refactor python shown at the top **
```python
promos = []

def promotion(promo_func):
  promos.append(promo_func)
  return promo_func

@promotion
def fidelity(order):
  """5% discount for customers with 1000 or more fidelity points"""
  return order.total() * .05 if order.customer.fidelity >= 1000 else 0

@promotion
def bulk_item(order):
  """10% discount for each LineItem with 20 or more units"""
  discount = 0
  for item in order.cart:
    if item.quantity >= 20:
      discount += item.total() * .1
  return discount

@promotion
def large_order(order):
  """7% discount for orders with 10 or more distinct items""" distinct_items = {item.product for item in order.cart}
  if len(distinct_items) >= 10:
    return order.total() * .07
  return 0

def best_promo(order):
  """Select best discount available"""
  return max(promo(order) for promo in promos)
```

### Variable scope rule:

When Python compiles the body of the function, it decides that b is a local variable because it is assigned within the function. The generated bytecode reflects this decision and will try to fetch b from the local environment.

```python
>>>b=6
>>> def f2(a):
...   print(a)
...   print(b)
...   b=9

>>> f2(3)
3
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<stdin>", line 3, in f2
UnboundLocalError: local variable 'b' referenced before assignment
```

if want to use global variable, add ``global``

```python
>>> def f3(a):
...   global b
...   print(a)
...   print(b)
...   b=9
>>> f3(3)
3
6
>>> b
9

```