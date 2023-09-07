
## Memorize Some Tips

### Literals

Literals are used to represent fixed values in Python. Here are some examples:

- Integer literals: `42`, `0b101010` (binary), `0o52` (octal), `0x2a` (hexadecimal)
- Numeric literals can also include underscores for readability: `123_456_789`, `0x1234_5678`, `0b111_00_101`, `123.789_012`

### Operations for Iterables

- Iteration: `for vars in s:`
- Variable unpacking: `v1, v2, ... = s`
- Membership: `x in s`, `x not in s`
- Expansion in list, tuple, or set literals: `[a, *s, b]`, `(a, *s, b)`, `{a, *s, b}`
- Throw variable: `throw variable like that`
- Example: `a,_,b=[1,2,3]`

### Set Operations

Set operations allow manipulating sets in Python:

```python
names1 = { 'IBM', 'MSFT', 'AA' }
names2 = set(['IBM', 'MSFT', 'HPE', 'IBM', 'CAT'])

a = names1 | names2      # Union: {'IBM', 'MSFT', 'HPE', 'AA', 'CAT'}
b = names1 & names2      # Intersection: {'IBM', 'MSFT'}
c = names1 - names2      # Difference: {'AA'}
d = names2 - names1      # Difference: {'HPE', 'CAT'}
e = names1 ^ names2      # Symmetric Difference: {'HPE', 'AA', 'CAT'}
```

### Discard()

The `discard()` method is used to remove an item from a set:

```python
s.discard('SCOX')  # Remove 'SCOX' if it exists.
```

### Dictionary Operations

Dictionaries offer various operations for manipulating key-value pairs:

- Get with default value: `p = prices.get('IBM', 0.0)`
- Delete: `del prices['GOOG']`
- Keys can be tuples: `prices[('IBM', '2015-02-03')] = 91.23`

### List Comprehension

List comprehension provides a concise way to create lists based on existing lists or other iterables:

```python
[expression for item1 in iterable1 if condition1
            for item2 in iterable2 if condition2
            ...
            for itemN in iterableN if conditionN]
```

This syntax is equivalent to the following code:

```python
result = []
for item1 in iterable1:
    if condition1:
        for item2 in iterable2:
            if condition2:
                ...
                for itemN in iterableN:
                    if conditionN:
                        result.append(expression)
```

### Generator Expression

Generator expressions are used to create generator objects, which generate values on the fly without storing them in memory:

```python
nums = [1, 2, 3, 4]
squares = (x*x for x in nums)

>>> squares
<generator object at 0x590a8>
>>> next(squares)
1
>>> next(squares)
4
```

### Python Enumerate

The `enumerate()` function is used to iterate over a sequence while keeping track of the index:

```python
for i, x in enumerate(s, start=100):
    statements
```

### Zip

The `zip()` function is used to iterate over multiple sequences simultaneously:

```python
for x, y in zip(s, t):
    statements
```

The `zip()` function returns an iterable of tuples.



## Exception Base Roots

- `BaseException`: The root class for all exceptions.
- `Exception`: Base class for all program-related errors.
- `ArithmeticError`: Base class for all math-related errors.
- `ImportError`: Base class for import-related errors.
- `LookupError`: Base class for all container lookup errors.
- `OSError`: Base class for all system-related errors. `IOError` and `EnvironmentError` are aliases.
- `ValueError`: Base class for value-related errors, including Unicode-related errors.
- `UnicodeError`: Base class for Unicode string encoding-related errors.
- `AssertionError`: Raised when an `assert` statement fails.
- `AttributeError`: Raised when a bad attribute lookup is performed on an object.
- `EOFError`: Raised when the end of a file is reached.
- `MemoryError`: Raised when a recoverable out-of-memory error occurs.
- `NameError`: Raised when a name is not found in the local or global namespace.
- `NotImplementedError`: Raised for an unimplemented feature.
- `RuntimeError`: A generic "something bad happened" error.
- `TypeError`: Raised when an operation is applied to an object of the wrong type.
- `UnboundLocalError`: Raised when a local variable is used before a value is assigned.
- `SystemExit`: Raised to indicate program exit.
- `KeyboardInterrupt`: Raised when a program is interrupted via Control-C.
- `StopIteration`: Raised to signal the end of iteration.

## New Exception

You can create your own custom exceptions by defining a new class that inherits from the `Exception` class. Here's an example:

```python
class NetworkError(Exception):
    pass

raise NetworkError('Cannot find host')
```

## Chained Exception

You can raise a different exception while preserving the original exception using the `from` keyword. Here's an example:

```python
try:
    # Some code that may raise an exception
except Exception as e:
    raise ValueError('An error occurred') from e
```

This creates a new `ValueError` exception with the original exception `e` chained to it.

| Exception Name         | Description                                       |
| ---------------------- | ------------------------------------------------- |
| BaseException          | The root class for all exceptions.                |
| Exception              | Base class for all program-related errors.        |
| ArithmeticError        | Base class for all math-related errors.           |
| ImportError            | Base class for import-related errors.             |
| LookupError            | Base class for all container lookup errors.       |
| OSError                | Base class for all system-related errors.         |
| ValueError             | Base class for value-related errors.              |
| UnicodeError           | Base class for Unicode string encoding errors.    |
| AssertionError         | Raised when an assert statement fails.            |
| AttributeError         | Raised when a bad attribute lookup is performed.  |
| EOFError               | Raised when the end of a file is reached.         |
| MemoryError            | Raised when a recoverable out-of-memory error occurs. |
| NameError              | Raised when a name is not found in the local or global namespace. |
| NotImplementedError   | Raised for an unimplemented feature.              |
| RuntimeError           | A generic "something bad happened" error.         |
| TypeError              | Raised when an operation is applied to an object of the wrong type. |
| UnboundLocalError      | Raised when a local variable is used before a value is assigned. |
| SystemExit             | Raised to indicate program exit.                  |
| KeyboardInterrupt     | Raised when a program is interrupted via Control-C. |
| StopIteration          | Raised to signal the end of iteration.            |

```python
class ApplicationError(Exception): pass

def do_something(): x = int('N/A') # raises ValueError

def spam(): try: do_something() except Exception as e: raise ApplicationError('It failed') from e

## Exception handling advice
```



## e.args
The tuple of arguments supplied when raising the exception. In most cases, this is a one-item tuple with a string describing the error. For OSError exceptions, the value is a 2-tuple or 3-tuple containing an integer error number, string error message, and an optional filename.

## e.__cause__
Previous exception if the exception was intentionally raised in response to handling another exception. See the later section on chained exceptions.

## e.__context__
Previous exception if the exception was raised while handling another exception.

## e.__traceback__
Stack traceback object associated with the exception.

```python
try:
    # do something
except (TypeError, ValueError) as e:
    # Handle Type or Value errors
```

```python
try:
    file = open('foo.txt', 'rt')
except FileNotFoundError as e:
    print(f'Unable to open foo: {e}')
    data = ''
else:
    data = file.read()
    file.close()
```

```python
file = open('foo.txt', 'rt')
try:
    # Do some stuff
    ...
finally:
    file.close()
```

Exception handling is one of the most difficult things to get right in larger programs. However, there are a few rules of thumb that make it easier.

The first rule is to not catch exceptions that can’t be handled at that specific location in the code. Consider a function like this:

```python
def read_data(filename):
    with open(filename, 'rt') as file:
        rows = []
        for line in file:
            row = line.split()
            rows.append((row[0], int(row[1]), float(row[2])))
    return rows
```

Suppose the `open()` function fails due to a bad filename. Is this an error that should be caught with a try-except statement in this function? Probably not. If the caller gives a bad filename, there is no sensible way to recover. There is no file to open, no data to read, and nothing else that’s possible. It’s better to let the operation fail and report an exception back to the caller. Avoiding an error check in `read_data()` doesn’t mean that the exception would never be handled anywhere—it just means that it’s not the role of `read_data()` to do it. Perhaps the code that prompted a user for a filename would handle this exception.

This advice might seem contrary to the experience of programmers accustomed to languages that rely upon special error codes or wrapped result types. In those languages, great care is made to make sure you always check return codes for errors on all operations. You don’t do this in Python. If an operation can fail and there’s nothing you can do to recover, it’s better to just let it fail. The exception will propagate to upper levels of the program where it is usually the responsibility of some other code to handle it.

On the other hand, a function might be able to recover from bad data. For example:

```python
def read_data(filename):
    with open(filename, 'rt') as file:
        rows = []
        for line in file:
            row = line.split()
            try:
                rows.append((row[0], int(row[1]), float(row[2])))
            except ValueError as e:
                print('Bad row:', row)
                print('Reason:', e)
    return rows
```

When catching errors, try to make your except clauses as narrow as reasonable. The above code could have been written to catch all errors by using `except Exception`. However, doing that would make the code catch legitimate programming errors that probably shouldn’t be ignored. Don’t do that—it will make debugging difficult.

Finally, if you’re explicitly raising an exception, consider making your own exception types. For example:

```python
# Code Termination
# exit code

# can be used instead of exit()

raise SystemExit()                      # Exit with no error message
raise SystemExit("Something is wrong")  # Exit with error
```

## Exception Hierarchy

- BaseException
- SystemExit
- KeyboardInterrupt
- GeneratorExit
- Exception
    - StopIteration
    - StopAsyncIteration
    - ArithmeticError
        - FloatingPointError
        - OverflowError
        - ZeroDivisionError
    - AssertionError
    - AttributeError
    - BufferError
    - EOFError
    - ImportError
        - ModuleNotFoundError
    - LookupError
        - IndexError
        - KeyError
    - MemoryError
    - NameError
        - UnboundLocalError
    - OSError
        - BlockingIOError
        - ChildProcessError
        - ConnectionError
            - BrokenPipeError
            - ConnectionAbortedError
            - ConnectionRefusedError
            - ConnectionResetError
        - FileExistsError
        - FileNotFoundError
        - InterruptedError
        - IsADirectoryError
        - NotADirectoryError
        - PermissionError
        - ProcessLookupError
        - TimeoutError
    - ReferenceError
    - RuntimeError
        - NotImplementedError
        - RecursionError
    - SyntaxError
        - IndentationError
            - TabError
    - SystemError
    - TypeError
    - ValueError
        - UnicodeError
            - UnicodeDecodeError
            - UnicodeEncodeError
            - UnicodeTranslateError
    - Warning
        - DeprecationWarning
        - PendingDeprecationWarning
        - RuntimeWarning
        - SyntaxWarning
        - UserWarning
        - FutureWarning
        - ImportWarning
        - UnicodeWarning
        - BytesWarning
        - EncodingWarning
        - ResourceWarning
```



## Class Definitions

```python
class NetworkError(Exception):
    pass

class DeviceError(Exception):
    def __init__(self, errno, msg):
        self.args = (errno, msg)
        self.errno = errno
        self.errmsg = msg
```

## Context Manager

```python
class ListTransaction:
    def __init__(self, thelist):
        self.thelist = thelist

    def __enter__(self):
        self.workingcopy = list(self.thelist)
        return self.workingcopy

    def __exit__(self, type, value, tb):
        if type is None:
            self.thelist[:] = self.workingcopy
        return False
```

This class allows you to make a sequence of modifications to an existing list. However, the modifications only take effect if no exceptions occur. Otherwise, the original list is left unmodified.

```python
items = [1, 2, 3]
with ListTransaction(items) as working:
    working.append(4)
    working.append(5)

print(items)  # Produces [1, 2, 3, 4, 5]

try:
    with ListTransaction(items) as working:
        working.append(6)
        working.append(7)
        raise RuntimeError("We're hosed!")
```

## Python Optimized Mode

If you run Python with the `-o` option, it will run in optimized mode, but it won't check assertions.

## What is Object in Python

Every piece of data stored in a program is an object. Each object has an identity, a type (also known as its class), and a value. For example, when you write `a = 42`, an integer object is created with the value of 42. The identity of the object is a number representing its location in memory. `a` is a label that refers to this specific location, although the label is not part of the object itself. The type of an object, also known as the object's class, defines the object's internal data representation as well as supported methods. When an object of a particular type is created, that object is called an instance of that type. After an instance is created, its identity does not change. If an object's value can be modified, the object is said to be mutable. If the value cannot be modified, the object is said to be immutable. An object that holds references to other objects is said to be a container. Objects are characterized by their attributes. An attribute is a value associated with an object that is accessed using the dot operator (`. `). An attribute might be a simple data value, such as a number. However, an attribute could also be a function that is invoked to carry out some operation. Such functions are called methods.

The following example illustrates access to attributes:

```python
obj.attribute
```

A subtype is a type defined by inheritance. It carries all of the features of the original type plus additional and/or redefined methods. Inheritance is discussed in more detail in Chapter 7.

Although type checks can be added to a program, this is often not as useful as you might imagine. For one, excessive checking impacts performance. Second, programs don't always define objects that neatly fit into a nice type hierarchy. For instance, if the purpose of the `isinstance(items, list)` statement above is to test whether `items` is "list-like," it won't work with objects that have the same programming interface as a list but don't directly inherit from the built-in list type (one example is `deque` from the `collections` module).

## Reference Counting and Garbage Collection

Python manages objects through automatic garbage collection. All objects are reference-counted. An object's reference count is increased whenever it's assigned to a new name or placed in a data structure that references it. An object's reference count is decreased by the `del` statement or whenever a reference goes out of scope or is reassigned.

When an object's reference count reaches zero, it is garbage-collected. However, in some cases, a circular dependency may exist in a collection of objects that are no longer in use. In such cases, the destruction of the objects will be delayed until a cycle detector executes to find and delete the inaccessible objects. The exact behavior can be fine-tuned and controlled using functions in the `gc` standard library module. The `gc.collect()` function can be used to immediately invoke the cyclic garbage collector.

## First-Class Object

All objects in Python are said to be first-class. This means that all objects that can be assigned to a name can also be treated as data. As data, objects can be stored as variables, passed as arguments, returned from functions, compared against other objects, and more.

## Object Protocol and Data Abstraction

Unlike a compiler for a static language, Python does not verify correct program behavior in advance. Instead, the behavior of an object is determined by a dynamic process that involves the dispatch of so-called "special" or "magic" methods. The names of these special methods are always preceded and followed by double underscores (`__`). The methods are automatically triggered by the interpreter as a program executes. For example, the operation `x * y` is carried out by a method `x.__mul(y)`. The names of these methods and their corresponding operators are hard-wired. The behavior of any given object depends entirely on the set of special methods that it implements.

The next few sections describe the special methods associated with different categories of core interpreter features. These categories are sometimes called "protocols." An object, including a user-defined class, may define any combination of these features to make the object behave in different ways.
```

### Generate Markdown Table for Dunder

| Method                      | Description                                 |
|-----------------------------|---------------------------------------------|
| `__init__(self, *args, **kwargs)` | Initializes an instance.             |
| `__del__(self)`                    | Called when an instance is being destroyed.|
| `__repr__(self)`                   | Creates a string representation.           |
| `__new__(self)`                    | Creates a new instance.                    |

### Object Management Methods

| Method                 | Description                  |
|------------------------|------------------------------|
| `__add__(self, other)` | Adds two objects together.    |
| `__sub__(self, other)` | Subtracts one object from another. |
| `__mul__(self, other)` | Multiplies two objects.       |
| `__truediv__(self, other)` | Divides one object by another. |
| `__floordiv__(self, other)` | Performs floor division.    |
| `__mod__(self, other)` | Performs modulo operation.    |
| `__matmul__(self, other)` | Performs matrix multiplication. |



If `__bool__()` is undefined, then `__len__()` is used as a fallback. If both `__bool__()` and `__len__()` are undefined, an object is simply considered to be True.

The `__eq__()` method is used to determine basic equality for use with the `==` and `!=` operators. The default implementation of `__eq__()` compares objects by identity using the `is` operator. The `__ne__()` method, if present, can be used to implement special processing for `!=`, but is usually not required as long as `__eq__()` is implemented.

Matrices, returning a matrix with the results. If comparison is not possible, the methods should return the built-in object `NotImplemented`. This is not the same as the `NotImplementedError`.

It is not necessary for an ordered object to implement all of the comparison operations in Table 4.3. If you want to be able to sort objects or use functions such as `min()` or `max()`, then `__lt__()` must be minimally defined. If you are adding comparison operators to a user-defined class, the `@total_ordering` class decorator in the `functools` module may be of some use. It can generate all of the methods as long as you minimally implement `__eq__()` and one of the other comparisons.

The `__hash__()` method is defined on instances that are to be placed into a set or be used as keys in a mapping (dictionary). The value returned is an integer that should be the same for two instances that compare as equal. Moreover, `__eq__()` should always be defined together with `__hash__()` because the two methods work together. The value returned by `__hash__()` is typically used as an internal implementation detail of various data structures. However, it’s possible for two different objects to have the same hash value. Therefore, `__eq__()` is necessary to resolve potential collisions.

Conversion Protocols

- `__str__(self)`: Conversion to a string
- `__bytes__(self)`: Conversion to bytes
- `__format__(self, format_spec)`: Creates a formatted representation
- `__bool__(self)`: bool(self)
- `__int__(self)`: int(self)
- `__float__(self)`: float(self)
- `__complex__(self)`: __index__(self) Conversion to an integer index [self]

Examples of formatting:

- `f'{x:spec}'`: Calls `x.__format__('spec')`
- `format(x, 'spec')`: Calls `x.__format__('spec')`
- `'x is {0:spec}'.format(x)`: Calls `x.__format__('spec')`

The `__index__()` method performs an integer conversion of an object when it’s used in an operation that requires an integer value. This includes indexing in sequence operations. For example, if `items` is a list, performing an operation such as `items[x]` will attempt to execute `items[x.__index__()]` if `x` is...

Container Protocols

- `__len__(self)`: Returns length
- `__getitem__(self, key)`: Returns `self[key]`
- `__setitem__(self, key, value)`: Sets `self[key] = value`
- `__delitem__(self, key)`: Deletes `self[key]`
- `__contains__(self, obj)`: `obj in self`

Here’s an example:

```python
a = [1, 2, 3, 4, 5, 6]
len(a)               # a.__len__()
x = a[2]             # x = a.__getitem__(2)
a[1] = 7             # a.__setitem__(1, 7)
del a[2]             # a.__delitem__(2)
5 in a               # a.__contains__(5)
```

Slicing operations such as `x = s[i:j]` are also implemented using `__getitem__()`, `__setitem__()`, and `__delitem__()`. For slices, a special slice instance is passed as the key. This instance has attributes that describe the range of the slice being requested. For example:

```python
a = [1, 2, 3, 4, 5, 6]
x = a[1:5]           # x = a.__getitem__(slice(1, 5, None))
a[1:3] = [10, 11, 12]  # a.__setitem__(slice(1, 3, None), [10, 11, 12])
del a[1:4]           # a.__delitem__(slice(1, 4, None))
```

The slicing features of Python are more powerful than many programmers realize. For example, the following variations of extended slicing are all supported and may be useful for working with multidimensional data structures such as matrices and arrays:

```python
a = m[0:100:10]          # Strided slice (step=10)
b = m[1:10, 3:20]        # Multidimensional slice
c = m[0:100:10, 50:75:5] # Multiple dimensions with strides
m[0:5, 5:10] = n         # Extended slice assignment
del m[:10, 15:]          # Extended slice deletion
```

### Iterator Protocol

If an instance, `obj`, supports iteration, it provides a method, `obj.iter()`, that returns an iterator. An iterator `iter`, in turn, implements a single method, `iter.next()`, that returns the next object or raises `StopIteration` to signal the end of iteration. These methods are used by the implementation of the `for` statement as well as other operations that implicitly perform iteration. For example, the statement `for x in s` is carried out by performing these steps:

```python
_iter = s.__iter__()
while True:
    try:
         x = _iter.__next__()
    except StopIteration:
         break
    # Do statements in body of for loop
```

**Sample Iterator**

```python
class FRange:
    def __init__(self, start, stop, step):
        self.start = start
        self.stop = stop
        self.step = step

    def __iter__(self):
        x = self.start
        while x < self.stop:
            yield x
            x += self.step

# Example use:
nums = FRange(0.0, 1.0, 0.1)
for x in nums:
    print(x)     # 0.0, 0.1, 0.2, 0.3, ...
```

### Attribute Access

- `__getattribute__(self, name)`: Returns the attribute `self.name`
- `__getattr__(self, name)`: Returns the attribute `self.name` if it’s not found through `__getattribute__()`
- `__setattr__(self, name, value)`: Sets the attribute `self.name = value`
- `__delattr__(self, name)`

### Function Protocol

An object can emulate a function by providing the `__call__()` method. If an object, `x`, provides this method, it can be invoked like a function. That is, `x(arg1, arg2, ...)` invokes `x.__call__(arg1, arg2, ...)`. There are many built-in types that support function calls. For example, types implement `__call__()` to create new instances. Bound methods implement `__call__()` to pass the `self` argument to instance methods. Library functions such as `functools.partial()` also create objects that emulate functions.

### Context Manager Protocol

The `with` statement allows a sequence of statements to execute under the control of an instance known as a context manager. The general syntax is as follows:

```python
with context [ as var]:
     statements
```

A context object shown here is expected to implement the

### Use `repr`

Just use `repr` it's good for debugging in the REPL.

### Docs

Docstring is stored in the `__doc__` attribute. The documentation string is stored in the `doc` attribute of the function. It’s often accessed by IDEs to provide interactive help. Functions can also be annotated with type hints. For example:

### Passing Arguments

You can pass arguments like this:

```python
def func(x, y, z):
    ...
s = (1, 2, 3)
# Pass a sequence as arguments
result = func(*s)
# Pass a mapping as keyword arguments
d = { 'x':1, 'y':2, 'z':3 }
result = func(**d)
```

### Tuple Example

```python
from typing import NamedTuple

class ParseResult(NamedTuple):
    name: str
    value: str

def parse_value(text):
    '''
    Split text of the form name=val into (name, val)
    '''
    parts = text.split('=', 1)
    return ParseResult(parts[0].strip(), parts[1].strip())

r = parse_value('url=http://www.python.org')
print(r.name, r.value)
```

### Avoid Using Global Statement

It should be noted that use of the global statement is usually considered poor Python style. If you’re writing code where a function needs to mutate state behind the scenes, consider using a class definition and modify state by mutating an instance or class variable instead. For example:

```python
class Config:
    x = 42

def func():
    Config.x = 13
```

Python allows nested function definitions. Here’s an example:

### Inner Functions

`nonlocal` cannot be used to refer to a global variable—it must reference a local variable in an outer scope. Thus, if a function is assigning to a global, you should still use the global declaration.

Use of nested functions and `nonlocal` declarations is not a common programming style. For example, inner functions have no outside visibility, which can complicate testing and debugging. Nevertheless, nested functions are sometimes useful for breaking recursion.

- Current limit: `sys.getrecursionlimit()` default is 1000
- Set limit: `sys.setrecursionlimit()`

### Lambda Functions

```python
x = 2
f = lambda y: x * y
x = 3
g = lambda y: x * y
print(f(10))       # --> prints 30
print(g(10))       # --> prints 30
```

This is called late binding.

```python
x = 2
f = lambda y, x=x: x * y
x = 3
g = lambda y, x=x: x * y
```

### Higher-Order Functions

Python supports the concept of higher-order functions. This means that functions can be passed as arguments to other functions, placed in data structures, and returned by a function as a result. Functions are said to be first-class objects, meaning there is no difference between how you might handle a function and any other kind of data.

### Function as Callback with Parameters

```python
after(10, lambda: add(2, 3))

from functools import partial
after(10, partial(add, 2, 3))
```

Since partials are fully evaluated, the callables created by `partial()` are objects that can be serialized into bytes, saved in files, and even transmitted across network connections (for example, using the `pickle` standard library module). This is not possible with a lambda function. Thus, in applications where functions are passed around, possibly to Python interpreters running in different processes or on different machines, you’ll find `partial()` to be a bit more adaptable. As an aside, partial function application is closely related to a 




## Decorators

**Shorthand of Decorators**

```python
func = decorate(func)
```

```python
from functools import wraps

def trace(func):
    @wraps(func)
    def call(*args, **kwargs):
        print('Calling', func.__name__)
        return func(*args, **kwargs)
    return call
```

The `@wraps()` decorator copies various function metadata to the replacement function. In this case, metadata from the given function `func()` is copied to the returned wrapper function `call()`.

**Multiple Decorators**

```python
@decorator1
@decorator2
def func(x):
    pass
```

The above code is equivalent to:

```python
func = decorator1(decorator2(func))
```

## Function Inspections

- `f.__name__`: Function name
- `f.__qualname__`: Fully qualified name (if nested)
- `f.__module__`: Name of module in which defined
- `f.__doc__`: Documentation string
- `f.__annotations__`: Type hints
- `f.__globals__`: Dictionary that is the global namespace
- `f.__closure__`: Closure variables (if any)
- `f.__code__`: Underlying code object

## Check if Two Function Parameters are the Same

```python
import inspect

def func(x: int, y: float, debug=False) -> float:
    pass

sig = inspect.signature(func)

assert inspect.signature(func1) == inspect.signature(func2)
```

Attributes are not visible within the function body—they are not local variables and do not appear as names in the execution environment. The main use of function attributes is to store extra metadata. Sometimes frameworks or various metaprogramming techniques utilize function tagging—that is, attaching attributes to functions. One example is the `@abstractmethod` decorator that’s used on methods within abstract base classes.

```python
def func():
    statements

func.secure = 1
func.private = 1
```

## Frame Attributes

- `f.f_back`: Previous stack frame (toward the caller)
- `f.f_code`: Code object being executed
- `f.f_locals`: Dictionary of local variables (`locals()`)
- `f.f_globals`: Dictionary used for global variables (`globals()`)
- `f.f_builtins`: Dictionary used for built-in names
- `f.f_lineno`: Line number
- `f.f_lasti`: Current instruction. This is an index into the bytecode string of `f_code`.
- `f.f_trace`: Function called at the start of each source code line

```python
import inspect
from collections import ChainMap

def debug(*varnames):
    f = inspect.currentframe().f_back  # Previous stack
    vars = ChainMap(f.f_locals, f.f_globals)
    print(f'{f.f_code.co_filename}:{f.f_lineno}')
    for name in varnames:
        print(f'    {name} = {vars[name]!r}')

# Example use
def func(x, y):
    z = x + y
    debug('x', 'y')  # Shows x and y along with file/line
```

## Dynamic Code Execution

```python
exec(str [, globals [, locals]])
```

```python
globs = {'x': 7,
         'y': 10,
         'birds': ['Parrot', 'Swallow', 'Albatross']
         }
locs = {}
exec('z = 3 * x + 4 * y', globs, locs)
exec('for b in birds: print(b)', globs, locs)
```

```python
def make_init(*names):
    parms = ','.join(names)
    code = f'def __init__(self, {parms}):\n'
    for name in names:
        code += f' self.{name} = {name}\n'
    d = {}
    exec(code, d)
    return d['__init__']

# Example use
class Vector:
    __init__ = make_init('x', 'y', 'z')
```

## Positional and Named Arguments

```python
def func(x, y, /):
    pass

func(1, 2)     # Ok
func(1, y=2)   # Error
```

## Name and Docstring

- `__name__`
- `__doc__`

## Argument Passing

Everything is passed by reference, but extra care is needed only for mutable types. Pass ready parameters to functions.

```python
def func(x, y, z):
    ...

s = (1, 2, 3)
# Pass a sequence as arguments
result = func(*s)
# Pass a mapping as keyword arguments
d = {'x': 1, 'y': 2, 'z': 3}
result = func(**d)
```

## NamedTuple

```python
from typing import NamedTuple

class ParseResult(NamedTuple):
    name: str
    value: str

def parse_value(text):
    '''
    Split text of the form name=val into (name, val)
    '''
    parts = text.split('=', 1)
    return ParseResult(parts[0].strip(), parts[1].strip())
```

## Late Binding

```python
def func():
    n += 1    # Error: UnboundLocalError
```

```python
x = 42

def func():
    print(x)    # Fails. UnboundLocalError
    x = 13
```

## Async Function

Use of `await` is only valid within an enclosing async function definition. It’s also a required part of making async functions execute. If you leave off the `await`, you’ll find that the code breaks. The requirement of using `await` hints at a general usage issue with asynchronous functions. Namely, their different evaluation model prevents them from being used in combination with other parts of Python. Specifically, it is never possible to write code like `print(await twice(2))`—at least not without an intervening `await` or `async` keyword.

```python
async def twice(x):
    return 2 * x

def main():
    print(twice(2))         # Error. Doesn't execute the function
    print(await twice(2))   # Error. Can't use await here.
```

## `yield` and `return`

```python
def func():
    try:
        next(f)
    except StopIteration as e:
        yield 37
        return 42
```

```python
def countdown(n):
    print('Counting down from', n)
    try:
        while n > 0:
            yield n
            n = n - 1
    finally:
        print('Only made it to', n)
```

Generators are guaranteed to execute the `finally` block code even if the generator is not fully consumed—it will execute when the abandoned generator is garbage-collected. Similarly, any cleanup code involving a context manager is also guaranteed to execute.

## `yield from`

```python
def countup(stop):
    n = 1
    while n <= stop:
        yield n
        n += 1

def countdown(start):
    n = start
    while n > 0:
        yield n
        n -= 1

def up_and_down(n):
    yield from countup(n)
    yield from countdown(n)
```

`yield from` is especially useful when writing code that must recursively iterate through nested iterables.

```python
def flatten(items):
    for i in items:
        if isinstance(i, list):
            yield from flatten(i)
        else:
            yield i
```

## Avoiding Recursion Limit

```python
def flatten(items):
    stack = [iter(items)]
    while stack:
        try:
            item = next(stack[-1])
            if isinstance(item, list):
                stack.append(iter(item))
            else:
                yield item
        except StopIteration:
            stack.pop()
```

## Sending Values to Enhanced Generators (Coroutines)

```python
def receiver():
    print('Ready to receive')
    while True:
        n = yield
        print('Got', n)
```

```python
r = receiver()
r.send(None)        # Advances to the first yield
print(r.send(1))
print(r.send(2))
print(r.send('Hello'))
```



# Check `throw()` and `close()` Method in Internet

## Enhanced Generators

Enhanced generators are an odd programming construct. Unlike a simple generator which naturally feeds a for loop, there is no core language feature that drives an enhanced generator. Why, then, would you ever want a function that needs values to be sent to it? Is it purely academic? Historically, enhanced generators have been used in the context of concurrency libraries—especially those based on asynchronous I/O. In that context, they’re usually referred to as *coroutines* or *generator-based coroutines*. However, much of that functionality has been folded into the `async` and `await` features of Python. There is little practical reason to use `yield` for that specific use case. That said, there are still some practical applications. Like generators, an enhanced generator can be used to implement different kinds of evaluation and control flow. One example is the `@contextmanager` decorator found in the `contextlib` module.

```python
class Manager:
    def __enter__(self):
        return somevalue
    def __exit__(self, ty, val, tb):
        if ty:
            # An exception occurred
            ...
            # Return True/ if handled. False otherwise
```

With the `@contextmanager` generator, everything prior to the `yield` statement executes when the manager enters (via the `enter()` method). Everything after the `yield` executes when the manager exits (via the `exit()` method). If an error took place, it is reported as an exception on the `yield` statement. Here's a book on the internet where you can find more information about this topic.

## Final Words: A Brief History of Generators and Looking Forward

Generators are one of Python’s more interesting success stories. They are also part of a greater story concerning iteration. Iteration is one of the most common programming tasks of all. In early versions of Python, iteration was implemented via sequence indexing and the `__getitem__()` method. This later evolved into the current iteration protocol based on `__iter__()` and `__next__()` methods. Generators appeared shortly thereafter as a more convenient way to implement an iterator. In modern Python, there is almost no reason to ever implement an iterator using anything other than a generator. Even on iterable objects that you might define yourself, the `__iter__()` method itself is conveniently implemented in this way.

In later versions of Python, generators took on a new role as they evolved enhanced features related to coroutines—the `send()` and `throw()` methods. These were no longer limited to iteration but opened up possibilities for using generators in other contexts. Most notably, this formed the basis of many so-called *async* frameworks used for network programming and concurrency. However, as asynchronous programming has evolved, most of this has transformed into later features that use the `async`/`await` syntax. Thus, it’s not so common to see generator functions used outside of the context of iteration—their original purpose. In fact, if you find yourself defining a generator function and you’re not sure why, it’s worth questioning whether or not it’s necessary.

### Function Introspection

Here are some useful function introspection attributes:

- `f.__name__`: Function name
- `f.__qualname__`: Fully qualified name
- `f.__module__`: Module name
- `f.__doc__`: Docstring
- `f.__annotations__`: Type hints
- `f.__globals__`: Dictionary of global namespace
- `f.__closure__`: Closure variables
- `f.__code__`: Code object
