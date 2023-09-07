# Much about Classes and Object Oriented Programming

Classes are used to create new kinds of objects.

## Classes Example

```python
class AccountPortfolio:
    def __init__(self):
        self.accounts = []

    def add_account(self, account):
        self.accounts.append(account)

    def total_funds(self):
        return sum(account.inquiry() for account in self.accounts)

    def __len__(self):
        return len(self.accounts)

    def __getitem__(self, index):
        return self.accounts[index]

    def __iter__(self):
        return iter(self.accounts)
```

### Usage

```python
# Example
port = AccountPortfolio()
port.add_account(Account('Guido', 1000.0))
port.add_account(Account('Eva', 50.0))

print(port.total_funds())    # -> 1050.0
len(port)                    # -> 2

# Print the accounts
for account in port:
    print(account)

# Access an individual account by index
port[1].inquiry()            # -> 50.0
```

## Avoiding Inheritance via Composition

### Inheritance

```python
class Stack(list):
    def push(self, item):
        self.append(item)

# Example
s = Stack()
s.push(1)
s.push(2)
s.push(3)
s.pop()     # -> 3
s.pop()     # -> 2
```

### Composition

```python
class Stack:
    def __init__(self):
        self._items = list()

    def push(self, item):
        self._items.append(item)

    def pop(self):
        return self._items.pop()

    def __len__(self):
        return len(self._items)

# Example use
s = Stack()
s.push(1)
s.push(2)
s.push(3)
s.pop()    # -> 3
s.pop()     # -> 2
```

### Passing Container as Argument

```python
class Stack:
    def __init__(self, *, container=None):
        if container is None:
            container = list()
        self._items = container

    def push(self, item):
        self._items.append(item)

    def pop(self):
        return self._items.pop()

    def __len__(self):
        return len(self._items)

s = Stack(container=array.array('i'))
s.push(42)
s.push(23)
s.push('a lot')     # TypeError
```

This is also an example of what’s known as *dependency injection*. Instead of hardwiring Stack to depend on list, you can make it depend on any container a user decides to pass in, provided it implements the required interface.

## Avoid Inheritance via Functions

If there is too much plumbing going on here. If you’re writing a lot of single-method classes, consider using functions instead. 

### Class Based Parsing

```python
class DataParser:
    def parse(self, lines):
        records = []
        for line in lines:
            row = line.split(',')
            record = self.make_record(row)
            records.append(row)
        return records

    def make_record(self, row):
        raise NotImplementedError()

class PortfolioDataParser(DataParser):
    def make_record(self, row):
        return {
           'name': row[0],
           'shares': int(row[1]),
           'price': float(row[2])
        }

parser = PortfolioDataParser()
data = parser.parse(open('portfolio.csv'))
```

### Function Based Parsing

```python
def parse_data(lines, make_record):
    records = []
    for line in lines:
        row = line.split(',')
        record = make_record(row)
        records.append(row)
    return records

def make_dict(row):
    return {
        'name': row[0],
        'shares': int(row[1]),
        'price': float(row[2])
    }

data = parse_data(open('portfolio.csv'), make_dict)
```

# Dynamic Binding and Duck Typing

Dynamic binding is the runtime mechanism that Python uses to find the attributes of objects. This is what allows Python to work with instances without regard for their type. In Python, variable names do not have an associated type. Thus, the attribute binding process is independent of what kind of object `obj` is. If you make a lookup, such as `obj.name`, it will work on any `obj` whatsoever that happens to have a `name` attribute. This behavior is sometimes referred to as **duck typing**—in reference to the adage “if it looks like a duck, quacks like a duck, and walks like a duck, then it’s a duck.”

Python programmers often write programs that rely on this behavior. For example, if you want to make a customized version of an existing object, you can either inherit from it, or you can create a completely new object that looks and acts like it but is otherwise unrelated. This latter approach is often used to maintain loose coupling of program components. For example, code may be written to work with any kind of object whatsoever as long as it has a certain set of methods. One of the most common examples is with various iterable objects defined in the standard library. There are all sorts of objects that work with the `for` loop to produce values—lists, files, generators, strings, and so on. However, none of these inherit from any kind of special `Iterable` base class. They merely implement the methods required to perform iteration—and it all works.

## Don't Inherit Builtin Types

`dict`, `list` they are written in C and bypass `__setitem__` and `__getitem__`.

If you want to use `UserDict`, import it like this: `from collections import UserDict`.

## Class Variables vs Methods

In a class definition, all functions are assumed to operate on an instance, which is always passed as the first parameter `self`. However, the class itself is also an object that can carry state and be manipulated as well. As an example, you could track how many instances have been created using a class variable `num_accounts`:

```python
class Account:
    num_accounts = 0

    def __init__(self, owner, balance):
        self.owner = owner
        self.balance = balance
        Account.num_accounts += 1

    def __repr__(self):
        return f'{type(self).__name__}({self.owner!r}, {self.balance!r})'

    def deposit(self, amount):
        self.balance += amount

    def withdraw(self, amount):
        self.deposit(-amount)    # Must use self.deposit()

    def inquiry(self):
        return self.balance
```

Class variables are defined outside the normal `__init__()` method. To modify them, use the class, not `self`. For example:

```python
>>> a = Account('Guido', 1000.0)
>>> b = Account('Eva', 10.0)
>>> Account.num_accounts
2
```

## `classmethod` Usage: Alternative Way of Creating a Class

```python
class Account:
    def __init__(self, owner, balance):
        self.owner = owner
        self.balance = balance

    @classmethod
    def from_xml(cls, data):
        from xml.etree.ElementTree import XML
        doc = XML(data)
        return cls(doc.findtext('owner'),
                   float(doc.findtext('amount')))

# Example use

data = '''
<account>
    <owner>Guido</owner>
    <amount>1000.0</amount>
</account>
'''
a = Account.from_xml(data)
```

## Configuration of Classes

```python
import time

class Date:
    datefmt = '{year}-{month:02d}-{day:02d}'
    def __init__(self, year, month, day):
        self.year = year
        self.month = month
        self.day = day

    def __str__(self):
        return self.datefmt.format(year=self.year,
                                   month=self.month,
                                   day=self.day)

    @classmethod
    def from_timestamp(cls, ts):
        tm = time.localtime(ts)
        return cls(tm.tm_year, tm.tm_mon, tm.tm_mday)

    @classmethod
    def today(cls):
        return cls.from_timestamp(time.time())
```

This class features a class variable `datefmt` that adjusts output from the `__str__()` method. This is something that can be customized via inheritance:

```python
class MDYDate(Date):
    datefmt = '{month}/{day}/{year}'

class DMYDate(Date):
    datefmt = '{day}/{month}/{year}'

# Example
a = Date(1967, 4, 9)
print(a)       # 1967-04-09

b = MDYDate(1967, 4, 9)
print(b)       # 4/9/1967

c = DMYDate(1967, 4, 9)
print(c)      # 9/4/1967
```

## `dict.from_keys()` Example

```python
dict.from_keys(['a','b','c'], 0)
# Output: {'a': 0, 'b': 0, 'c': 0}
```

## Static Methods

```python
class StandardPolicy:
    @staticmethod
    def deposit(account, amount):
        account.balance += amount

    @staticmethod
    def withdraw(account, amount):
        account.balance -= amount

    @staticmethod
    def inquiry(account):
        return account.balance

class EvilPolicy(StandardPolicy):
    @staticmethod
    def deposit(account, amount):
        account.balance += 0.95*amount

    @staticmethod
    def inquiry(account):
        if random.randint(0,4) == 1:
           return 1.10 * account.balance
        else:
           return account.balance

class Account:
    def __init__(self, owner, balance, *, policy=StandardPolicy):
        self.owner = owner
        self.balance = balance
        self.policy = policy

    def __repr__(self):
        return f'Account({self.policy}, {self.owner!r}, {self.balance!r})'

    def deposit(self, amount):
        self.policy.deposit(self, amount)

    def withdraw(self, amount):
        self.policy.withdraw(self, amount)

    def inquiry(self):
        return self.policy.inquiry(self)
```


# About Design Patterns

In writing object-oriented programs, programmers sometimes get fixated on implementing named design patterns—such as the *strategy pattern*, the *flyweight pattern*, the *singleton pattern*, and so forth. Many of these originate from the famous *Design Patterns* book by Erich Gamma, Richard Helm, Ralph Johnson, and John Vlissides.

If you are familiar with such patterns, the general design principles used in other languages can certainly be applied to Python. However, many of these documented patterns are aimed at working around specific issues that arise from the strict static type system of C++ or Java. The dynamic nature of Python renders a lot of these patterns obsolete, an overkill, or simply unnecessary.

That said, there are a few overarching principles of writing good software—such as striving to write code that is debuggable, testable, and extensible. Basic strategies such as writing classes with useful `__repr__()` methods, preferring composition over inheritance, and allowing dependency injection can go a long way towards these goals. Python programmers also like to work with code that can be said to be *Pythonic*. Usually, that means that objects obey various built-in protocols, such as iteration, containers, or context management. For example, instead of trying to implement some exotic data traversal pattern from a Java programming book, a Python programmer would probably implement it with a generator function feeding a `for` loop, or just replace the entire pattern with a few dictionary lookups.

## Properties

As noted in the previous section, Python places no runtime restrictions on attribute values or types. However, such enforcement is possible if you put an attribute under the management of a so-called *property*. A property is a special kind of attribute that intercepts attribute access and handles it via user-defined methods. These methods have complete freedom to manage the attribute as they see fit. Here is an example:

```python
import string

class Account:
    def __init__(self, owner, balance):
        self.owner = owner
        self._balance = balance

    @property
    def owner(self):
        return self._owner

    @owner.setter
    def owner(self, value):
        if not isinstance(value, str):
            raise TypeError('Expected str')
        if not all(c in string.ascii_uppercase for c in value):
            raise ValueError('Must be uppercase ASCII')
        if len(value) > 10:
            raise ValueError('Must be 10 characters or less')
        self._owner = value

class SomeClass:
    @property
    def attr(self):
        print('Getting')

    @attr.setter
    def attr(self, value):
        print('Setting', value)

    @attr.deleter
    def attr(self):
        print('Deleting')

# Example
s = SomeClass()
s.attr         # Getting
s.attr = 13    # Setting
del s.attr     # Deleting
```

## Types, Interfaces, Abstract Classes

```python
class A:
    pass

class B(A):
    pass

class C:
    pass

a = A()           # Instance of 'A'
b = B()           # Instance of 'B'
c = C()           # Instance of 'C'

type(a)           # Returns the class object A
isinstance(a, A)  # Returns True
isinstance(b, A)  # Returns True, B derives from A
isinstance(b, C)  # Returns False, B not derived from C
```

**Note**: ABC must be implemented.

```python
from abc import ABC, abstractmethod

class Stream(ABC):
    @abstractmethod
    def receive(self):
        pass

    @abstractmethod
    def send(self, msg):
        pass

    @abstractmethod
    def close(self):
        pass
```


# Multiple Inheritance and Mixins

## Interfaces using ABC Classes

```python
from abc import ABC, abstractmethod

class Stream(ABC):
    @abstractmethod
    def receive(self):
        pass

    @abstractmethod
    def send(self, msg):
        pass

    @abstractmethod
    def close(self):
        pass

class Iterable(ABC):
    @abstractmethod
    def __iter__(self):
        pass


class MessageStream(Stream, Iterable):
    def receive(self):
        ...

    def send(self):
        ...

    def close(self):
        ...

    def __iter__(self):
        ...
```

`m = MessageStream()`

`isinstance(m, Stream)     # -> True`

`isinstance(m, Iterable)   # -> True`

## Mixin Classes

The other use of multiple inheritance is to define mixin classes. A mixin class is a class that modifies or extends the functionality of other classes. Consider the following class definitions:

```python
class Duck:
    def noise(self):
        return 'Quack'

    def waddle(self):
        return 'Waddle'

class Trombonist:
    def noise(self):
        return 'Blat!'

    def march(self):
        return 'Clomp'

class Cyclist:
    def noise(self):
        return 'On your left!'

    def pedal(self):
        return 'Pedaling'
```

These classes are completely unrelated to each other. There is no inheritance relationship and they implement different methods. However, there is a shared commonality in that they each define a `noise()` method. Using that as a guide, you could define the following classes:

```python
class LoudMixin:
    def noise(self):
        return super().noise().upper()

class AnnoyingMixin:
    def noise(self):
        return 3 * super().noise()

class LoudDuck(LoudMixin, Duck):
    pass

class AnnoyingTrombonist(AnnoyingMixin, Trombonist):
    pass

class AnnoyingLoudCyclist(AnnoyingMixin, LoudMixin, Cyclist):
    pass
```

`d = LoudDuck()`

`d.noise()  # -> 'QUACK'`

`t = AnnoyingTrombonist()`

`t.noise()  # -> 'Blat!Blat!Blat!'`

`c = AnnoyingLoudCyclist()`

`c.noise()  # -> 'ON YOUR LEFT!ON YOUR LEFT!ON YOUR LEFT!'`

Since mixin classes are defined in the same way as normal classes, it is best to include the word "Mixin" as part of the class name. This naming convention provides a greater clarity of purpose.

To fully understand mixins, you need to know a bit more about how inheritance and the `super()` function work.

First, whenever you use inheritance, Python builds a linear chain of classes known as the Method Resolution Order, or MRO for short. This is available as the `mro` attribute on a class. Here are some examples for single inheritance:

```python
class Base:
    pass

class A(Base):
    pass

class B(A):
    pass

Base.__mro__  # -> (<class 'Base'>, <class 'object'>)
A.__mro__     # -> (<class 'A'>, <class 'Base'>, <class 'object'>)
B.__mro__     # -> (<class 'B'>, <class 'A'>, <class 'Base'>, <class 'object'>)
```

# Class Decorators and Class Methods

## Factory function that uses the registry
```python
def create_decoder(mimetype):
    return _registry[mimetype]()
```

```python
@register_decoder
class TextDecoder:
    mimetypes = [ 'text/plain' ]
    def decode(self, data):
        ...
```

```python
@register_decoder
class HTMLDecoder:
    mimetypes = [ 'text/html' ]
    def decode(self, data):
        ...
```

```python
@register_decoder
class ImageDecoder:
    mimetypes = [ 'image/png', 'image/jpg', 'image/gif' ]
    def decode(self, data):
        ...
```

## Example usage
```python
decoder = create_decoder('image/jpg')
```

A class decorator is free to modify the contents of the class it’s given. For example, it might even rewrite existing methods. This is a common alternative to mixin classes or multiple inheritance. For example, consider these decorators:

### decorator override function

```python
def loud(cls):
    orig_noise = cls.noise
    def noise(self):
        return orig_noise(self).upper()
    cls.noise = noise
    return cls

def annoying(cls):
    orig_noise = cls.noise
    def noise(self):
        return 3 * orig_noise(self)
    cls.noise = noise
    return cls

@annoying
@loud
class Cyclist(object):
    def noise(self):
        return 'On your left!'

    def pedal(self):
        return 'Pedaling'
```

### Add code to class at runtime

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __repr__(self):
        return f'{type(self).__name__}({self.x!r}, {self.y!r})'
```

Writing such methods is often annoying. Perhaps a class decorator could create the method for you?

```python
import inspect
def with_repr(cls):
    args = list(inspect.signature(cls).parameters)
    argvals = ', '.join('{self.%s!r}' % arg for arg in args)
    code = 'def __repr__(self):\n'
    code += f' return f'{cls.__name__}({argvals})'\n'
    locs = { }
    exec(code, locs)
    cls.__repr__ = locs['__repr__']
    return cls

# Example
@with_repr
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y
```

In this example, a repr() method is generated from the calling signature of the init() method. The method is created as a text string and passed to exec() to create a function. That function is attached to the class.

Similar code generation techniques are used in parts of the standard library. For example, a convenient way to define data structures is to use a dataclass:

```python
from dataclasses import dataclass

@dataclass
class Point:
    x: int
    y: int
```

A dataclass automatically creates methods such as __init__() and __repr__() from class type hints. The methods are created using exec(), similarly to the prior example. Here’s how the resulting Point class works:

```python
p = Point(2, 3)
p
```

Output:
```
Point(x=2, y=3)
```


One downside of such an approach is poor startup performance. Dynamically creating code with `exec()` bypasses the compilation optimizations that Python normally applies to modules. Defining a large number of classes in this way may therefore significantly slow down the importing of your code.

## Supervised Inheritance - `__init_subclass__`

As you saw in the previous section, sometimes you want to define a class and perform additional actions. A class decorator is one mechanism for doing this. However, a parent class can also perform extra actions on behalf of its subclasses. This is accomplished by implementing an `__init_subclass__(cls)` class method. For example:

```python
class Base:
    @classmethod
    def __init_subclass__(cls):
        print('Initializing', cls)

# Example (should see 'Initializing' message for each class)
class A(Base):
    pass

class B(A):
    pass
```

If an `__init_subclass__()` method is present, it is triggered automatically upon the definition of any child class. This happens even if the child is buried deeply in an inheritance hierarchy.

Many of the tasks commonly performed with class decorators can be performed with `__init_subclass__()` instead. For example, class registration:

```python
class DecoderBase:
    _registry = { }
    @classmethod
    def __init_subclass__(cls):
        for mt in cls.mimetypes:
            DecoderBase._registry[mt.mimetype] = cls

# Factory function that uses the registry
def create_decoder(mimetype):
    return DecoderBase._registry[mimetype]()

class TextDecoder(DecoderBase):
    mimetypes = [ 'text/plain' ]
    def decode(self, data):
        ...

class HTMLDecoder(DecoderBase):
    mimetypes = [ 'text/html' ]
    def decode(self, data):
        ...

class ImageDecoder(DecoderBase):
    mimetypes = [ 'image/png', 'image/jpg', 'image/gif' ]
    def decode(self, data):
        ...
```

## Class Initialization with `__repr__`

```python
import inspect
class Base:
    @classmethod
    def __init_subclass__(cls):
        # Create a __repr__ method
        args = list(inspect.signature(cls).parameters)
        argvals = ', '.join('{self.%s!r}' % arg for arg in args)
        code = 'def __repr__(self):\n'
        code += f' return f'{cls.__name__}({argvals})'\n'
        locs = { }
        exec(code, locs)
        cls.__repr__ = locs['__repr__']

class Point(Base):
    def __init__(self, x, y):
        self.x = x
        self.y = y
```

If multiple inheritance is being used, you should use `super()` to make sure all classes that implement `__init_subclass__()` get called. For example:

```python
class A:
    @classmethod
    def __init_subclass__(cls):
        print('A.init_subclass')
        super().__init_subclass__()

class B:
    @classmethod
    def __init_subclass__(cls):
        print('B.init_subclass')
        super().__init_subclass__()

# Should see output from both classes here
class C(A, B):
    pass
```

## Object Life Cycle and Memory Management

When a class is defined, the resulting class is a factory for creating new instances. For example:

```python
class Account:
    def __init__(self, owner, balance):
        self.owner = owner
        self.balance = balance
```

Create some `Account` instances:
```python
a = Account('Guido', 1000.0)
b = Account('Eva', 25.0)
```

The creation of an instance is carried out in two steps using the special method `__new__()` that creates a new instance and `__init__()` that initializes it. For example, the operation `a = Account('Guido', 1000.0)` performs these steps:

```python
a = Account.__new__(Account, 'Guido', 1000.0)
if isinstance(a, Account):
    Account.__init__(a, 'Guido', 1000.0)
```

Except for the first argument which is the class instead of an instance, `__new__()` normally receives the same arguments as `__init__()`. However, the default implementation of `__new__()` just ignores them. You’ll sometimes see `__new__()` invoked with just a single argument. For example, this code also works:

```python
a = Account.__new__(Account)
if isinstance(a, Account):
    Account.__init__(a, 'Guido', 1000.0)
```

Direct use of the `__new__()` method is uncommon, but sometimes it’s used to create instances while bypassing the invocation of the `__init__()` method. One such use is in class methods.
```python
class Spam:
    @classmethod
    def bar(cls, *args, **kwargs):
        return cls.__new__(cls, *args, **kwargs)
```


```python
import time

class Date:
    def __init__(self, year, month, day):
        self.year = year
        self.month = month
        self.day = day

    @classmethod
    def today(cls):
        t = time.localtime()
        self = cls.__new__(cls)   # Make instance
        self.year = t.tm_year
        self.month = t.tm_month
        self.day = t.tm_day
        return self
```

Modules that perform object serialization such as `pickle` also utilize `new()` to recreate instances when objects are deserialized. This is done without ever invoking `init()`.

Sometimes a class will define `new()` if it wants to alter some aspect of instance creation. Typical applications include instance caching, singletons, and immutability. As an example, you might want `Date` class to perform date interning—that is, caching and reusing `Date` instances that have an identical year, month, and day. Here is one way that might be implemented:

```python
class Date:
    _cache = { }

    @staticmethod
    def __new__(cls, year, month, day):
        self = Date._cache.get((year,month,day))
        if not self:
            self = super().__new__(cls)
            self.year = year
            self.month = month
            self.day = day
            Date._cache[year,month,day] = self
        return self

    def __init__(self, year, month, day):
        pass
```

In this example, the class keeps an internal dictionary of previously created `Date` instances. When creating a new `Date`, the cache is consulted first. If a match is found, that instance is returned. Otherwise, a new instance is created and initialized.

A subtle detail of this solution is the empty `init()` method. Even though instances are cached, every call to `Date()` still invokes `init()`. To avoid duplicated effort, the method simply does nothing—instance creation actually takes place in `new()` when an instance is created the first time.

There are ways to avoid the extra call to `init()` but it requires sneaky tricks. One way to avoid it is to have `new()` return an entirely different type instance—for example, one belonging to a different class. Another solution, described later, is to use a metaclass.

Once created, instances are managed by reference counting. If the reference count reaches zero, the instance is immediately destroyed. When the instance is about to be destroyed, the interpreter first looks for a `del()` method associated with the object and calls it. For example:

```python
class Account(object):
    def __init__(self, owner, balance):
        self.owner = owner
        self.balance = balance

    def __del__(self):
        print('Deleting Account')
```

Occasionally, a program will use the `del` statement to delete a reference to an object as shown. If this causes the reference count of the object to reach zero, the `del()` method is called. However, in general, the `del` statement doesn’t directly call `del()` because there may be other object references living elsewhere. There are many other ways that an object might be deleted—for example, reassignment of a variable name or a variable going out of scope in a function:

```python
>>> a = Account('Guido', 1000.0)
>>> a = 42
Deleting Account
>>> def func():
...     a = Account('Guido', 1000.0)
...
>>> func()
Deleting Account
```

In practice, it’s rarely necessary for a class to define a `del()` method. The only exception is when the destruction of an object requires an extra cleanup action—such as closing a file, shutting down a network connection, or releasing other system resources. Even in these cases, it’s dangerous to rely on `del()` for a proper shutdown because there’s no guarantee that this method will be called when you think it would. For clean shutdown of resources, you should give the object an explicit `close()` method. You should also make your class support the context manager protocol so it can be used with the `with` statement. Here is an example that covers all of the cases:

```python
class SomeClass:
    def __init__(self):
        self.resource = open_resource()

    def __del__(self):
        self.close()

    def close(self):
        self.resource.close()

    def __enter__(self):
        return self

    def __exit__(self, ty, val, tb):
        self.close()

# Closed via __del__()
s = SomeClass()
del s

# Explicit close
s = SomeClass()
s.close()

# Closed at the end of a context block
with SomeClass() as s:
    ...
```

Again, it should be emphasized that writing a `del()` in a class is almost never necessary. Python already has garbage collection and there is simply no need to do it unless there is some extra action that needs to take place upon object destruction. Even then, you still might not need `del()` as it’s possible that the object is already programmed to clean itself up properly even if you do nothing.

As if there weren’t enough dangers with reference counting and object destruction, there are certain kinds of programming patterns—especially those involving parent-child relationships, graphs, or caching—where objects can create a so-called reference cycle.
```python
class SomeClass:
    def __del__(self):
        print('Deleting')

parent = SomeClass()
child = SomeClass()
```


# Create a child-parent reference cycle
parent.child = child
child.parent = parent

# Try deletion (no output from __del__ appears)
del parent
del child

In this example, the variable names are destroyed but you never see execution of the `del()` method. The two objects each hold internal references to each other, so there’s no way for the reference count to ever drop to 0. To handle this, a special cycle-detecting garbage collector runs every so often. Eventually the objects will be reclaimed, but it’s hard to predict when this might happen. If you want to force garbage collection, you can call `gc.collect()`. The `gc` module has a variety of other functions related to the cyclic garbage collector and monitoring memory.

Because of the unpredictable timing of garbage collection, the `del()` method has a few restrictions placed on it. First, any exception that propagates out of `del()` is printed to `sys.stderr`, but otherwise ignored. Second, the `del()` method should avoid operations such as acquiring locks or other resources. Doing so could result in a deadlock when `del()` is unexpectedly fired in the middle of executing an unrelated function within the seventh inner callback circle of signal handling and threads. If you must define `del()`, keep it simple.

**weak references**

Sometimes objects are kept alive when you’d much rather see them die. In an earlier example, a `Date` class was shown with internal caching of instances. One problem with this implementation is that there is no way for an instance to ever be removed from the cache. As such, the cache will grow larger and larger over time.

One way to fix this problem is to create a weak reference using the `weakref` module. A weak reference is a way of creating a reference to an object without increasing its reference count. To work with a weak reference, you have to add an extra bit of code to check if the object being referred to still exists. Here’s an example of how you create a weakref:

```python
import weakref
a_ref = weakref.ref(a)
```

In this example, `a_ref` is a weak reference to the object `a`. You can use the weak reference to access the object, but it doesn't prevent the object from being garbage collected.

```python
a = Account('Guido', 1000.0)
import weakref
a_ref = weakref.ref(a)
a_ref()
```

Support for weak references requires instances to have a mutable `weakref` attribute. Instances of user-defined classes normally have such an attribute by default. However, built-in types and certain kinds of special data structures—named tuples, classes with slots—do not. If you want to construct weak references to these types, you can do it by defining variants with a `weakref` attribute added:

```python
class wdict(dict):
    __slots__ = ('__weakref__',)

w = wdict()
w_ref = weakref.ref(w)      # Now works
```

**attribute binding**

The state associated with an instance is stored in a dictionary that’s accessible as the instance’s `__dict__` attribute. This dictionary contains the data that’s unique to each instance.

Classes are linked to their base classes by a special attribute `__bases__`, which is a tuple of the base classes. The `__bases__` attribute is only informational. The actual runtime implementation of inheritance uses the `__mro__` attribute which is a tuple of all parent classes listed in search order. This underlying structure is the basis for all operations that get, set, or delete the attributes of instances.

Whenever an attribute is set using `obj.name = value`, the special method `obj.__setattr__('name', value)` is invoked. If an attribute is deleted using `del obj.name`, the special method `obj.__delattr__('name')` is invoked. The default behavior of these methods is to modify or remove values from the local `__dict__` of `obj` unless the requested attribute happens to correspond to a property or descriptor. In that case, the set and delete operations will be carried out by the `__set__` and `__delete__` functions associated with the property.

For attribute lookup such as `obj.name`, the special method `obj.__getattribute__('name')` is invoked. This method carries out the search for the attribute, which normally includes checking the properties, looking in the local `__dict__`, checking the class dictionary, and searching the MRO. If this search fails, a final attempt to find the attribute is made by invoking the `obj.__getattr__('name')` method of the class (if defined). If this fails, an `AttributeError` exception is raised.

User-defined classes can implement their own versions of the attribute access functions, if desired. For example, here’s a class that restricts the attribute names that can be set:

```python
class Account:
    def __init__(self, owner, balance):
        self.owner = owner
        self.balance = balance

    def __setattr__(self, name, value):
        if name not in {'owner', 'balance'}:
            raise AttributeError(f'No attribute {name}')
        super().__setattr__(name, value)
```


# Example
a = Account('Guido', 1000.0)
a.balance = 940.25          # Ok
a.amount = 540.2            # AttributeError. No attribute amount

**proxies, wrappers, delegations**

A common implementation technique for proxies involves the `getattr()` method. Here is a simple example:

```python
class A:
    def spam(self):
        print('A.spam')

    def grok(self):
        print('A.grok')

    def yow(self):
        print('A.yow')

class LoggedA:
    def __init__(self):
        self._a = A()

    def __getattr__(self, name):
        print('Accessing', name)
        # Delegate to internal A instance
        return getattr(self._a, name)
```

In this example, the `LoggedA` class acts as a proxy for class `A`. When an attribute is accessed on an instance of `LoggedA`, the `__getattr__()` method is invoked. It prints the accessed attribute name and then delegates the attribute access to the internal instance of `A`.

**Example use:**

```python
a = LoggedA()
a.spam()       # prints 'Accessing spam' and 'A.spam'
a.yow()        # prints 'Accessing yow' and 'A.yow'
```

Delegation is sometimes used as an alternative to inheritance. Here is an example:

```python
class A:
    def spam(self):
        print('A.spam')

    def grok(self):
        print('A.grok')

    def yow(self):
        print('A.yow')

class B:
    def __init__(self):
        self._a = A()

    def grok(self):
        print('B.grok')

    def __getattr__(self, name):
        return getattr(self._a, name)
```

In this example, class `B` holds an internal reference to an instance of `A` and delegates attribute access to it. Methods defined in class `B` override the corresponding methods in class `A`, while all other methods are delegated to the internal instance of `A`.

**Example use:**

```python
b = B()
b.spam()      # -> A.spam
b.grok()      # -> B.grok   (redefined method)
b.yow()       # -> A.yow
```

The technique of forwarding attribute lookup via `__getattr__()` is a common technique. However, be aware that it does not apply to operations mapped to special methods. For example, consider this class:

```python
class ListLike:
    def __init__(self):
        self._items = list()

    def __getattr__(self, name):
        return getattr(self._items, name)
```

In this example, the `ListLike` class forwards all of the standard list methods to an inner list using `__getattr__()`. However, operations such as `len(a)` or `a[0]` fail because they are not mapped to special methods (`__len__()` and `__getitem__()`). To make those work, you would have to explicitly implement the required special methods.

To illustrate, here's an updated `ListLike` class that implements the necessary special methods:

```python
class ListLike:
    def __init__(self):
        self._items = list()

    def __getattr__(self, name):
        return getattr(self._items, name)

    def __len__(self):
        return len(self._items)

    def __getitem__(self, index):
        return self._items[index]

    def __setitem__(self, index, value):
        self._items[index] = value
```

# slots

The `slots` attribute is a definition hint that allows Python to make performance optimizations for both memory use and execution speed. It eliminates the need for a dictionary to store instance data and uses a more compact array-based data structure instead. Using `slots` can result in a substantial reduction in memory use and a modest improvement in execution time, especially in programs that create a large number of objects.

Here are some key points about `slots`:

- The `slots` attribute lists only the instance attributes and does not include methods, properties, class variables, or any other class-level attributes.
- If a class uses `slots`, any derived class must also define `slots` (even if empty) to take advantage of the benefits. Failure to do so will result in slower performance and increased memory usage.
- `slots` is not compatible with multiple inheritance. If multiple base classes with non-empty `slots` are specified, a `TypeError` will be raised.
- Code that relies on the underlying `__dict__` attribute of instances may break when `slots` is used.
- `slots` has no effect on the invocation of methods such as `__getattribute__()`, `__getattr__()`, and `__setattr__()` if they are redefined in a class. However, the absence of the instance `__dict__` attribute should be considered when implementing these methods.


# Descriptors

Descriptors provide a way to customize attribute access in Python by implementing the special methods `__get__()`, `__set__()`, and `__delete__()`. They are class-level objects that manage access to attributes. Properties are implemented using descriptors.

Here's an example of a descriptor class called `Typed`:

```python
class Typed:
    expected_type = object

    def __set_name__(self, cls, name):
        self.key = name

    def __get__(self, instance, cls):
        if instance:
            return instance.__dict__[self.key]
        else:
            return self

    def __set__(self, instance, value):
        if not isinstance(value, self.expected_type):
            raise TypeError(f'Expected {self.expected_type}')
        instance.__dict__[self.key] = value

    def __delete__(self, instance):
        raise AttributeError("Can't delete attribute")
```

In this example, the `Typed` class defines a descriptor that performs type checking when an attribute is assigned and raises an error if an attempt is made to delete the attribute. Subclasses like `Integer`, `Float`, and `String` specialize `Typed` to match specific types.

Descriptors are used by including them as class attributes in another class. For example:

```python
class Account:
    owner = String()
    balance = Float()

    def __init__(self, owner, balance):
        self.owner = owner
        self.balance = balance
```

In this case, the `Account` class uses the descriptors `String` and `Float` to automatically call the appropriate `__get__()`, `__set__()`, or `__delete__()` methods when accessing the `owner` and `balance` attributes.

Descriptors take precedence over items in the instance dictionary. Even if an instance dictionary has a matching entry, the descriptor's `__set__()` method will be invoked. For example:

```python
a = Account('Guido', 1000.0)
a.balance = 'a lot'  # Raises TypeError: Expected <class 'float'>
```

The `__get__(instance, cls)` method of a descriptor takes arguments for both the instance and the class. When invoked at the class level, the instance argument is `None`. The `__get__()` method typically returns the descriptor itself if no instance is provided.

```python
Account.balance  # Returns <__main__.Float object at 0x110606710>
```

## Method Descriptor

A descriptor that only implements `__get__()` is known as a method descriptor. It is mainly used to implement Python's various types of methods, such as instance methods, class methods, and static methods. The `__get__()` method of a method descriptor only gets invoked if there is no matching entry in the instance dictionary.

Here's an example of implementing `@classmethod` and `@staticmethod` using method descriptors:

```python
import types

class classmethod:
    def __init__(self, func):
        self.__func__ = func

    def __get__(self, instance, cls):
        return types.MethodType(self.__func__, cls)

class staticmethod:
    def __init__(self, func):
        self.__func__ = func

    def __get__(self, instance, cls):
        return self.__func__
```

Lazy Evaluation

Method descriptors can be used to implement lazy evaluation of attributes. By only computing and assigning the attribute value when it is accessed for the first time, we can save computational resources.

Here's an example of implementing lazy evaluation using a descriptor called `Lazy`:

```python
class Lazy:
    def __init__(self, func):
        self.func = func

    def __set_name__(self, cls, name):
        self.key = name

    def __get__(self, instance, cls):
        if instance:
            value = self.func(instance)
            instance.__dict__[self.key] = value
            return value
        else:
            return self
```

In this example, the `Lazy` descriptor is used in the `Rectangle` class to lazily compute the area and perimeter attributes:

```python
class Rectangle:
    def __init__(self, width, height):
        self.width = width
        self.height = height

    area = Lazy(lambda self: self.width * self.height)
    perimeter = Lazy(lambda self: 2*self.width + 2*self.height)
```

When the `area` or `perimeter` attributes are accessed for the first time, the corresponding lambda function is executed to compute the value. The computed value is then stored in the instance's `__dict__` attribute for future use.

## Class Definitions

The definition of a class is a dynamic process. When you define a class using the class statement, a new dictionary is created that serves as the local class namespace. The body of the class then executes as a script within this namespace. Eventually, the namespace becomes the `__dict__` attribute of the resulting class object.

Any legal Python statement is allowed in the body of a class. Normally, you just define functions and variables, but control flow, imports, nested classes, and everything else is allowed. For example, here is a class that conditionally defines methods:

```python
class Account:
    def __init__(self, owner, balance):
        self.owner = owner
        self.balance = balance

    if debug:
        import logging
        log = logging.getLogger(f'{__module__}.{__qualname__}')
        def deposit(self, amount):
            Account.log.debug('Depositing %f', amount)
            self.balance += amount

        def withdraw(self, amount):
            Account.log.debug('Withdrawing %f', amount)
            self.balance -= amount
    else:
        def deposit(self, amount):
            self.balance += amount

        def withdraw(self, amount):
            self.balance -= amount
```

In this example, a global variable `debug` is being used to conditionally define methods. The `__module__` and `__qualname__` variables are predefined strings that hold information about the class name and enclosing module. These can be used by statements in the class body. In this example, they're being used to configure the logging system. There are probably cleaner ways of organizing the above code, but the key point is that you can put anything you want in a class.

One critical point about class definition is that the namespace used to hold the contents of the class body is not a scope of variables. Any name that gets used within a method (such as `Account.log` in the above example) needs to be fully qualified.

If a function like `locals()` is used in a class body (but not inside a method), it returns the dictionary being used for the class namespace.

## Dynamic Class Creation

Normally, classes are created using the `class` statement, but this is not a requirement. As noted in the previous section, classes are defined by executing the body of a class to populate a namespace. If you're able to populate a dictionary with your own definitions, you can make a class without ever using the `class` statement. To do that, use `types.new_class()`:

```python
import types

# Some methods (not in a class)
def __init__(self, owner, balance):
    self.owner = owner
    self.balance = balance

def deposit(self, amount):
    self.balance -= amount

def withdraw(self, amount):
    self.balance += amount

methods = {
   '__init__': __init__,
   'deposit': deposit,
   'withdraw': withdraw,
}

Account = types.new_class('Account', (),
               exec_body=lambda ns: ns.update(methods))

# You now have a class
a = Account('Guido', 1000.0)
a.deposit(50)
a.withdraw(25)
```

Dynamic class creation may be useful if you want to create classes from data structures or generate classes programmatically. For example, in the section on descriptors, the following classes were defined:

```python
class Integer(Typed):
    expected_type = int

class Float(Typed):
    expected_type = float

class String(Typed):
    expected_type = str
```

This code is highly repetitive. A data-driven approach can be used to generate the classes dynamically:

```python
typed_classes = [
   ('Integer', int),
   ('Float', float),
   ('String', str),
   ('Bool', bool),
   ('Tuple', tuple),
]

globals().update(
   (name, types.new_class(name, (Typed,),
          exec_body=lambda ns: ns.update(expected_type=ty)))
   for name, ty in typed_classes)
```

In this example, the global module namespace is being updated with dynamically created classes using `types.new_class()`. The `typed_classes` list defines the names and expected types for each class. Each class is created by calling `types.new_class()` with the class name, base classes, and an `exec_body` function that updates the namespace with the expected type. The resulting classes are then added to the global namespace using `globals().update()`.

Sometimes you will see `type()` being used to dynamically create a class instead. For example:

```python
Account = type('Account', (), methods)
```

This works, but it doesn’t take into account some of the more advanced class machinery such as metaclasses. In modern code, try to use `types.new_class()` instead.


# Metaclasses

When you define a class in Python, the class definition itself becomes an object. Here's an example:

```python
class Account:
    def __init__(self, owner, balance):
        self.owner = owner
        self.balance = balance

    def deposit(self, amount):
        self.balance += amount

    def withdraw(self, amount):
        self.balance -= amount
```

To check if `Account` is an object, you can use the `isinstance` function:

```python
isinstance(Account, object)
```

If you think about this long enough, you will realize that if `Account` is an object, then something had to create it. This creation of the class object is controlled by a special kind of class called a metaclass. Simply put, a metaclass is a class that creates instances of classes.

In the preceding example, the metaclass that created `Account` is a built-in class called `type`. In fact, if you check the type of `Account`, you will see that it is an instance of `type`:

```python
type(Account)
```

It's a bit brain-bending, but it's similar to integers. For example, if you write `x = 42` and then look at `x.__class__`, you'll get `int`, which is the class that creates integers. Similarly, `type` makes instances of types or classes.

When a new class is defined with the `class` statement, a number of things happen. First, a new namespace for the class is created. Next, the body of the class is executed in this namespace. Finally, the class name, base classes, and populated namespace are used to create the class instance. The following code illustrates the low-level steps that take place:

```python
namespace = type.__prepare__('Account', ())

# Step 2: Execute the class body
exec('''
def __init__(self, owner, balance):
    self.owner = owner
    self.balance = balance

def deposit(self, amount):
    self.balance += amount

def withdraw(self, amount):
    self.balance -= amount
''', globals(), namespace)

# Step 3: Create the final class object
Account = type('Account', (), namespace)
```

In the definition process, there is interaction with the `type` class to create the class namespace and to create the final class object. The choice of using `type` can be customized - a class can choose to be processed by a different metaclass by specifying a different metaclass. This is done by using the `metaclass` keyword argument in inheritance:

```python
class Account(metaclass=type):
```

If no metaclass is given, the `class` statement examines the type of the first entry in the tuple of base classes (if any) and uses that as the metaclass. Therefore, if you write `class Account(object)`, the resulting `Account` class will have the same type as `object` (which is `type`). Note that classes that don't specify any parent at all always inherit from `object`, so this still applies.

To create a new metaclass, define a class that inherits from `type`. Within this class, you can redefine one or more methods that are used during the class creation process. Typically, this includes the `__prepare__()` method used to create the class namespace, the `__new__()` method used to create the class instance, the `__init__()` method called after a class has already been created, and the `__call__()` method used to create new instances. The following example implements a metaclass that merely prints the input arguments to each method so you can experiment:

```python
class mytype(type):
    # Creates the class namespace
    @classmethod
    def __prepare__(meta, clsname, bases):
        print('Preparing:', clsname, bases)
        return super().__prepare__(clsname, bases)

    # Creates the class instance after body has executed
    @staticmethod
    def __new__(meta, clsname, bases, namespace):
        print('Creating:', clsname, bases, namespace)
        return super().__new__(meta, clsname, bases, namespace)

    # Initializes the class instance
    def __init__(cls, clsname, bases, namespace):
        print('Initializing:', clsname, bases, namespace)
        super().__init__(clsname, bases, namespace)

    # Creates new instances of the class
    def __call__(cls, *args, **kwargs):
        print('Creating instance:', args, kwargs)
        return super().__call__(*args, **kwargs)
```



# Example

```python
class Base(metaclass=mytype):
    pass
```

The definition of the `Base` produces the following output:

```
# Preparing: Base ()
# Creating: Base () {'__module__': '__main__', '__qualname__': 'Base'}
# Initializing: Base () {'__module__': '__main__', '__qualname__': 'Base'}
```

```python
b = Base()
```

Creating instance: `()`.

One tricky facet of working with metaclasses is the naming of variables and keeping track of the various entities involved. In the above code, the `meta` name refers to the metaclass itself. The `cls` name refers to a class instance created by the metaclass. Although not used here, the `self` name refers to a normal instance created by a class.

Metaclasses propagate via inheritance. So, if you've defined a base class to use a different metaclass, all child classes will also use that metaclass. Try this example to see your custom metaclass at work:

```python
class Account(Base):
    def __init__(self, owner, balance):
        self.owner = owner
        self.balance = balance

    def deposit(self, amount):
        self.balance += amount

    def withdraw(self, amount):
        self.balance -= amount

print(type(Account))   # -> <class 'mytype'>
```

The primary use of metaclasses is in situations where you want to exert extreme low-level control over the class definition environment and creation process. Before proceeding, however, remember that Python already provides a lot of functionality for monitoring and altering class definitions (such as the `__init_subclass__()` method, class decorators, descriptors, mixins, and so on). Most of the time, you probably don't need a metaclass. That said, the next few examples show situations where a metaclass provides the only sensible solution.

One use of a metaclass is in rewriting the contents of the class namespace prior to the creation of the class object. Certain features of classes are established at definition time and can't be modified later. One such feature is `__slots__`. As noted earlier, `__slots__` is a performance optimization related to the memory layout of instances. Here's a metaclass that automatically sets the `__slots__` attribute based on the calling signature of the `__init__()` method.

```python
import inspect

class SlotMeta(type):
    @staticmethod
    def __new__(meta, clsname, bases, methods):
        if '__init__' in methods:
            sig = inspect.signature(methods['__init__'])
            __slots__ = tuple(sig.parameters)[1:]
        else:
            __slots__ = ()
        methods['__slots__'] = __slots__
        return super().__new__(meta, clsname, bases, methods)

class Base(metaclass=SlotMeta):
    pass
```

# Example

```python
class Point(Base):
    def __init__(self, x, y):
        self.x = x
        self.y = y
```

In this example, the `Point` class that's created is automatically created with slots of `('x', 'y')`. The resulting instances of `Point` now get memory savings without knowing that slots are being used. It doesn't have to be specified directly. This kind of trick is not possible with class decorators or with `init_subclass()` because those features only operate on a class after it's been created. By then, it's too late to apply the slots optimization.

Another use of metaclasses is for altering the class definition environment. For example, duplicate definitions of a name during class definition normally result in a silent error - the second definition overwrites the first. Suppose you wanted to catch that. Here's a metaclass that does that by defining a different kind of dictionary for the class namespace:

```python
class NoDupeDict(dict):
    def __setitem__(self, key, value):
        if key in self:
            raise AttributeError(f'{key} already defined')
        super().__setitem__(key, value)

class NoDupeMeta(type):
    @classmethod
    def __prepare__(meta, clsname, bases):
        return NoDupeDict()

class Base(metaclass=NoDupeMeta):
    pass
```

# Example

```python
class SomeClass(Base):
    def yow(self):
        print('Yow!')

    def yow(self, x):             # Fails. Already defined
        print('Different Yow!')
```

This is only a small sample of what's possible. For framework builders, metaclasses offer an opportunity to tightly control what happens during class definition - allowing classes to serve as a kind of domain-specific language.

Historically, metaclasses have been used to accomplish a variety of tasks that are now possible through other means. The `init_subclass()` method, in particular, can be used to address a wide variety of use cases where metaclasses were once applied. This includes registration of classes with a central registry, automatic decoration of methods, and code generation.


## Built-in Objects for Instances and Classes

| Attribute              | Description                                        |
|------------------------|----------------------------------------------------|
| `cls.__name__`         | Class name                                         |
| `cls.__module__`       | Module name in which the class is defined          |
| `cls.__qualname__`     | Fully qualified class name                         |
| `cls.__bases__`        | Tuple of base classes                              |
| `cls.__mro__`          | Method Resolution Order tuple                       |
| `cls.__dict__`         | Dictionary holding class methods and variables     |
| `cls.__doc__`          | Documentation string                               |
| `cls.__annotations__`  | Dictionary of class type hints                      |
| `cls.__abstractmethods__` | Set of abstract method names (may be undefined if there aren't any) |

| Attribute              | Description                                        |
|------------------------|----------------------------------------------------|
| `i.__class__`          | Class to which the instance belongs                |
| `i.__dict__`           | Dictionary holding instance data (if defined)      |

