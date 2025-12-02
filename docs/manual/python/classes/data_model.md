
# Python Data Model

When using a framework, we spend a lot of time coding methods that are called by the framework. The same happens when we leverage the Python Data Model. The Python interpreter invokes special methods to perform basic object operations, often triggered by special syntax. The special method names are always written with leading and trailing double underscores (i.e., `__getitem__`). For example, the syntax `obj[key]` is supported by the `__getitem__` special method. In order to evaluate `my_collection[key]`, the interpreter calls `my_collection.getitem(key)`.

The special method names allow your objects to implement, support, and interact with fundamental language constructs such as:

- Collections
- Attribute access
- Iteration (including asynchronous iteration using `async for`)
- Operator overloading
- Function and method invocation
- String representation and formatting
- Asynchronous programming using `await`
- Object creation and destruction
- Managed contexts (including asynchronous context managers using `async with`)

## Example

```python
import collections
Card = collections.namedtuple('Card', ['rank', 'suit'])

class FrenchDeck:
    ranks = [str(n) for n in range(2, 11)] + list('JQKA')
    suits = 'spades diamonds clubs hearts'.split()
    
    def __init__(self):
        self._cards = [Card(rank, suit) for suit in self.suits for rank in self.ranks]
        
    def __len__(self):
        return len(self._cards)
        
    def __getitem__(self, position):
        return self._cards[position]
```

| Special Methods     | Description                                          |
|---------------------|------------------------------------------------------|
| `__iter__`          | Iterable                                             |
| `__len__`           | Sized                                                |
| `__contains__`      | Container                                            |
|                     |                                                      |
| Strings/bytes       |                                                      |
| `__repr__`          | String representation                                |
| `__str__`           | String representation                                |
| `__format__`        | Formatting                                           |
| `__bytes__`         | Bytes representation                                 |
| `__fspath__`        | File path representation                             |
|                     |                                                      |
| Number              |                                                      |
| `__abs__`           | Absolute value                                       |
| `__bool__`          | Boolean value                                        |
| `__complex__`       | Complex number                                       |
| `__int__`           | Integer representation                               |
| `__float__`         | Float representation                                 |
| `__hash__`          | Hash value                                           |
| `__index__`         | Indexing                                             |
|                     |                                                      |
| Collections         |                                                      |
| `__len__`           | Length                                               |
| `__getitem__`       | Item access                                          |
| `__setitem__`       | Item assignment                                      |
| `__delitem__`       | Item deletion                                        |
| `__contains__`      | Membership test                                      |
|                     |                                                      |
| Iteration           |                                                      |
| `__iter__`          | Iteration                                            |
| `__aiter__`         | Asynchronous iteration                               |
| `__next__`          | Next item                                            |
| `__anext__`         | Asynchronous next item                               |
| `__reversed__`      | Reversed iteration                                   |
|                     |                                                      |
| Callable or coroutine |                                                    |
| `__call__`          | Function or method invocation                        |
| `__await__`         | Asynchronous await                                   |
|                     |                                                      |
| Context managers    |                                                      |
| `__enter__`         | Context manager enter                                |
| `__aenter__`        | Asynchronous context manager enter                   |
| `__exit__`          | Context manager exit                                 |
| `__aexit__`         | Asynchronous context manager exit                    |
|                     |                                                      |
| Instance creation and destruction |                                             |
| `__new__`           | Object creation                                      |
| `__init__`          | Object initialization                                |
| `__del__`           | Object destruction                                   |
|                     |                                                      |
| Attribute management |                                                      |
| `__getattr__`       | Attribute retrieval                                  |
| `__getattribute__`  | Attribute access                                     |
| `__setattr__`       | Attribute assignment                                 |
| `__delattr__`       | Attribute deletion                                   |
| `__dir__`           | Directory listing                                    |
|                     |                                                      |
| Attribute descriptors |                                                    |
| `__get__`           | Descriptor get                                       |
| `__set__`           | Descriptor set                                       |
| `__delete__`        | Descriptor deletion                                  |
| `__set_name__`      | Descriptor set name                                  |
|                     |                                                      |
| Class services      |                                                      |
| `__prepare__`       | Class creation                                       |
| `__init_subclass__` | Subclass initialization                              |
| `__instancecheck__` | Instance check                                       |
| `__subclasscheck__` | Subclass check                                       |


Why `len` is not a method

`len` runs very fast when `x` is a built-in type. No method is called for built-in types in CPython; length is simply read from a field from the C struct. `len` is not called as methods, but in our Python objects, it works as normal.

### Data Structure
Every Python object in a C struct has two fields:

- `ob_refcnt` and `ob_fval`: reference count and pointer value.

### Mutable Sequences vs Immutable
- Mutable: list, bytearray, array.array, collections.deque, and memoryview.
- Immutable: tuple, str, and bytes.

**TIP**
In Python code, line breaks are ignored inside pairs of [], {}, or (). So you can build multiline lists, listcomps, genexps, dictionaries, and the like without using the ugly \ line continuation escape. Also, when those delimiters are used to define a literal with a comma-separated series of items, a trailing comma will be ignored. So, for example, when coding a multi-line list literal, it is thoughtful to put a comma after the last item.

### List Comps Versus `map` and `filter`
`map` and `filter` were faster, but nowadays they are the same.

**Tuple** is not just immutable lists.
It can be used as immutable lists or records with no field names (1,2) lat long.

If you write internationalized software, `_` is not a good dummy variable because it is traditionally used as an alias to the `gettext.gettext` function, as recommended in the `gettext` module documentation. Otherwise, it’s a conventional name for a placeholder variable to be ignored.

**Tuple as Immutable List**
1. Clarity: You know it never changes.
2. Performance: It uses less memory.

Are tuples more efficient than lists?

Raymond Hettinger answers:
- To evaluate a tuple, Python generates bytecode in constant one operation, but for a list, it pushes every element as a separate constant to data stacks and builds the list.
- Hashable tuple: `tuple(t)` returns a reference to the same `t`. No need to copy; the list makes a copy anyway.
- For fixed length, exact memory is allocated. The list has room to spare for the future.
- References to items of a tuple are stored in an array with the tuple struct itself. The list holds a pointer to the array of references stored elsewhere and makes the CPU cache less effective. But it is necessary because of the need to make room.

**Slicing**
`seq[start:stop:step]` - Python calls `seq.getitem(slice(start, stop, step))`.

**Building a List of Objects**
```
my_list = [[]] * 3 # same board appended, one changes everyone changes

board = [['_'] * 3 for i in range(3)] # no problem
```

**When List is Not the Answer**
If it contains the same type, maybe `array.array` will be better. You can dump it to a binary file directly, and it's memory-efficient.

**Queue**
Why don't use List as a queue? Because every item has to be shifted in memory.

Use `collections.deque` instead; it is thread-safe and has the `maxlen` attribute.
There are more queues:
- `queue`: `SimpleQueue`, `Queue`, `LifoQueue`, and `PriorityQueue`.
- `multiprocessing`: `SimpleQueue` and bounded `Queue` - very similar to those in the `queue` package but designed for interprocess communication. A specialized `multiprocessing.JoinableQueue`.
- `asyncio`: Provides `Queue`, `LifoQueue`, `PriorityQueue`, and `JoinableQueue`.
- `heap`: `heappush`, `heappop`.

**Flat vs Container Sequence**
Flat is all the same type.

**`hash()`**
Calling `hash(t)` on a tuple is a quick way to assert that its value is fixed. A `TypeError` will be raised if `t` contains mutable items.

**Decode vs Encode**
Imagine `str` is human-readable bytes; don't. Bytes need decoding; string encoding.

