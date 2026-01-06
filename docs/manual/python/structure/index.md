## What is an Object

Every piece of data stored in a program is an object. Each object has an identity, a type (also known as its class), and a value. For example, when you write `a = 42`, an integer object is created with the value of 42. The identity of the object is a number representing its location in memory, and `a` is a label that refers to this specific location. The type of an object defines its internal data representation and supported methods. An object can be mutable or immutable, and it can hold references to other objects.

Objects are characterized by their attributes, which are accessed using the dot operator (`.`). An attribute can be a simple data value or a function called a method. Inheritance allows the creation of subtype objects that inherit features from the original type and can have additional or redefined methods.

Type checks in a program may not always be useful due to performance impact and complex object hierarchies. For example, the `isinstance(items, list)` statement may not work for objects that have a list-like interface but don't directly inherit from the built-in list type.

## First-Class Object

All objects in Python are considered first-class objects. This means they can be assigned to names, stored as variables, passed as arguments, returned from functions, compared with other objects, and more. They can be treated as data and manipulated in various ways.

## Reference Counting and Garbage Collection

Python manages objects through automatic garbage collection. Objects are reference-counted, meaning their reference count increases when they are assigned to names or placed in data structures. The reference count decreases when references go out of scope, are reassigned, or deleted. When an object's reference count reaches zero, it is garbage-collected.

In some cases, circular dependencies among objects can lead to delayed destruction. The cyclic garbage collector detects and deletes these inaccessible objects periodically. Manual deletion of objects may be necessary in certain situations, and the `gc` module provides functions to control the garbage collection process.

## Object Protocol

Python's behavior is determined by dynamic processes involving special methods known as "magic" methods. These methods are automatically triggered by the interpreter during program execution. Special methods are denoted by double underscores (`__`) before and after the method name.

Different categories of objects have associated special methods called "protocols." For example, container objects define methods like `__len__()`, `__getitem__()`, `__setitem__()`, and `__delitem__()` to implement container operations such as indexing and slicing. Iterators implement the `__iter__()` and `__next__()` methods to enable iteration.

Other protocols include class attribute protocol, function protocol, context manager protocol, repr and doc protocol, and spread with `*`.

## Container Protocols

Container objects implement various special methods to support container operations:

```python
a = [1, 2, 3, 4, 5, 6]
len(a)               # a.__len__()
x = a[2]             # x = a.__getitem__(2)
a[1] = 7             # a.__setitem__(1,7)
del a[2]             # a.__delitem__(2)
5 in a               # a.__contains__(5)
```

Slicing operations are implemented using `__getitem__()`, `__setitem__()`, and `__delitem__()` methods. Slices are represented by special slice instances.

## Iterator Protocol

Objects that support iteration implement the iterator protocol:

```python
obj = iter(iterable)  # obj = iterable.__iter__()
next(obj)             # obj.__next__()
```

The `iter()` method returns an iterator object, which has a `__next__()` method to retrieve the next object in the iteration. The `for` statement implicitly performs iteration using these methods.

## Class Attribute Protocol

Objects define class attribute methods for accessing, setting, and deleting attributes:

```python
obj.__getattribute__(self, name)    # Returns the attribute self.name
obj.__getattr__(self, name)         # Returns the attribute self.name (if not found through __getattribute__())
obj.__setattr__(self, name, value)  # Sets the attribute self.name = value
obj.__delattr__(self, name)         # Deletes the attribute self.name
```

## Function Protocol

Objects can emulate functions by implementing the `__call__()` method. When an object provides this method, it can be invoked like a function:

```python
obj(arg1, arg2, ...)  # obj.__call__(arg1, arg2, ...)
```

Many built-in types and libraries support function calls by implementing `__call__()`.

## Context Manager Protocol

Context managers define the methods `__enter__()` and `__exit__()` (or `__aenter__()` and `__aexit__` for async context managers). These methods are used for resource management and provide a convenient way to set up and clean up resources within a block of code.

## Repr and Doc

Objects can define the `__repr__()` method to control how they are represented when using `print()` or `str()`. The `__doc__` attribute stores docstrings associated with the object.

## Spread with *

The `*` operator can be used to pass sequences or mappings as arguments to functions:

```python
def func(x, y, z):
    ...

s = (1, 2, 3)
result = func(*s)  # Pass a sequence as arguments

d = { 'x': 1, 'y': 2, 'z': 3 }
result = func(**d)  # Pass a mapping as keyword arguments
```

Next: [Code Design](code_design.md)
