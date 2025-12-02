# Tables Extracted from Python Built-ins and Standard Library Documentation

## Table 10.1: Operations on Bytes and Bytearrays

| Operation               | Description                                            |
|-------------------------|--------------------------------------------------------|
| `s + t`                 | Concatenates if `t` is bytes.                          |
| `s * n`                 | Replicates if `n` is an integer.                       |
| `s % x`                 | Formats bytes. `x` is tuple.                           |
| `s[i]`                  | Returns element `i` as an integer.                     |
| `s[i:j]`                | Returns a slice.                                       |
| `s[i:j:stride]`         | Returns an extended slice.                             |
| `len(s)`                | Number of bytes in `s`.                                |
| `s.capitalize()`        | Capitalizes the first character.                       |
| `s.center(width [, pad])` | Centers the string in a field of length `width`.     |
| `s.count(sub [, start [, end]])` | Counts occurrences of the specified substring `sub`. |
| `s.decode([encoding [, errors]])` | Decodes a byte string into text (bytes type only). |
| `s.endswith(suffix [, start [, end]])` | Checks the end of the string for a suffix. |
| `s.expandtabs([tabsize])` | Replaces tabs with spaces.                          |
| `s.find(sub [, start [, end]])` | Finds the first occurrence of `sub`.          |
| `s.hex()`               | Converts to a hexadecimal string.                      |
| `s.index(sub [, start [, end]])` | Finds the first occurrence or error of `sub`. |
| `s.isalnum()`           | Checks if all characters are alphanumeric.             |
| `s.isalpha()`           | Checks if all characters are alphabetic.               |
| `s.isascii()`           | Checks if all characters are ASCII.                    |
| `s.isdigit()`           | Checks if all characters are digits.                   |
| `s.islower()`           | Checks if all characters are lowercase.                |
| `s.isspace()`           | Checks if all characters are whitespace.               |
| `s.istitle()`           | Checks if the string is title-cased.                   |
| `s.isupper()`           | Checks if all characters are uppercase.                |
| `s.join(t)`             | Joins a sequence of strings `t` using delimiter `s`.   |
| `s.ljust(width [, fill])` | Left-aligns `s` in field of `width`.               |
| `s.lower()`             | Converts to lowercase.                                 |
| `s.lstrip([chrs])`      | Removes leading whitespace or specified characters.    |
| `s.maketrans(x [, y [, z]])` | Makes translation table for `s.translate()`.     |
| `s.partition(sep)`      | Partitions based on `sep`; returns tuple `(head, sep, tail)`. |
| `s.removeprefix(prefix)` | Removes prefix if present.                            |
| `s.removesuffix(suffix)` | Removes suffix if present.                            |
| `s.replace(old, new [, maxreplace])` | Replaces substring occurrences.          |
| `s.rfind(sub [, start [, end]])` | Finds last occurrence of `sub`.             |
| `s.rindex(sub [, start [, end]])` | Last occurrence or raises error.            |
| `s.rjust(width [, fill])` | Right-aligns `s` in field of `width`.              |
| `s.rpartition(sep)`     | Partitions `s` from the end based on `sep`.            |
| `s.rsplit([sep [, maxsplit]])` | Splits string from right with delimiter `sep`. |
| `s.rstrip([chrs])`      | Removes trailing whitespace or specified characters.   |
| `s.split([sep [, maxsplit]])` | Splits string using delimiter `sep`.           |
| `s.splitlines([keepends])` | Splits string into list of lines.                  |
| `s.startswith(prefix [, start [, end]])` | Checks if string starts with `prefix`. |
| `s.strip([chrs])`       | Removes leading and trailing whitespace/characters.    |
| `s.swapcase()`          | Swaps case of characters in string.                    |
| `s.title()`             | Returns title-cased version of string.                 |
| `s.translate(table [, deletechars])` | Translates string using table, removing `deletechars`. |
| `s.upper()`             | Converts string to uppercase.                          |
| `s.zfill(width)`        | Pads string on left with zeros up to `width`.          |

---

## Table 10.2: Additional Operations on Byte Arrays

| Operation                | Description                                           |
|--------------------------|-------------------------------------------------------|
| `s[i] = v`                | Item assignment.                                      |
| `s[i:j] = t`              | Slice assignment.                                     |
| `s[i:j:stride] = t`       | Extended slice assignment.                            |
| `del s[i]`                | Item deletion.                                        |
| `del s[i:j]`              | Slice deletion.                                       |
| `del s[i:j:stride]`       | Extended slice deletion.                              |
| `s.append(x)`             | Appends a new byte to the end.                        |
| `s.clear()`               | Clears the byte array.                                |
| `s.copy()`                | Makes a copy.                                         |
| `s.extend(t)`             | Extends `s` with bytes from `t`.                      |
| `s.insert(n, x)`          | Inserts byte `x` at index `n`.                        |
| `s.pop([n])`              | Removes and returns byte at index `n`.                |
| `s.remove(x)`             | Removes first occurrence of byte `x`.                 |
| `s.reverse()`             | Reverses the byte array in-place.                     |

---

## Table 10.4: Operations on Dictionaries

| Operation                | Description                                                 |
|--------------------------|-------------------------------------------------------------|
| `m | n`                  | Merges dictionaries `m` and `n`.                            |
| `len(m)`                 | Returns the number of items in `m`.                         |
| `m[k]`                   | Returns the item of `m` with key `k`.                       |
| `m[k] = x`               | Sets item `k` in `m` to `x`.                                |
| `del m[k]`               | Removes key `k` and its value from `m`.                     |
| `k in m`                 | Checks if `k` is a key in `m`.                              |
| `m.clear()`              | Removes all items from `m`.                                 |
| `m.copy()`               | Makes a shallow copy of `m`.                                |
| `m.fromkeys(s [, value])`| Creates new dict with keys from `s`, all values set to `value`. |
| `m.get(k [, v])`         | Returns `m[k]` if exists; otherwise returns `v`.            |
| `m.items()`              | Returns an iterable of `(key, value)` pairs in `m`.         |
| `m.keys()`               | Returns an iterable of keys in `m`.                         |
| `m.pop(k [, default])`   | Removes key `k` and returns its value, or `default`.        |
| `m.popitem()`            | Removes and returns an arbitrary `(key, value)` pair.       |
| `m.setdefault(k [, v])`  | Returns `m[k]` if exists; otherwise sets `m[k] = v` and returns `v`. |
| `m.update(b)`            | Updates `m` with key/value pairs from mapping `b`.          |
| `m.values()`             | Returns an iterable of values in `m`.                       |

---

## Table 10.8: Set Operations and Methods

| Operation           | Description                            |
|---------------------|----------------------------------------|
| `s | t`             | Union of sets `s` and `t`.            |
| `s & t`             | Intersection of `s` and `t`.          |
| `s - t`             | Difference of `s` and `t`.            |
| `s ^ t`             | Symmetric difference of `s` and `t`.  |
| `len(s)`            | Number of items in set `s`.           |
| `s.add(item)`       | Adds `item` to set `s`.               |
| `s.clear()`         | Removes all items from `s`.           |
| `s.copy()`          | Returns a shallow copy of `s`.        |
| `s.difference(t)`   | Items in `s` not in `t`.              |
| `s.difference_update(t)` | Removes items in both `s` and `t` from `s`. |
| `s.discard(item)`   | Removes `item` from `s` if present.   |
| `s.intersection(t)` | Items common to `s` and `t`.          |
| `s.intersection_update(t)` | Updates `s` with items common to `s` and `t`. |
| `s.isdisjoint(t)`   | True if `s` and `t` share no items.   |
| `s.issubset(t)`     | True if all items of `s` are in `t`.  |
| `s.issuperset(t)`   | True if all items of `t` are in `s`.  |
| `s.pop()`           | Removes and returns an arbitrary item from `s`. |
| `s.remove(item)`    | Removes `item` from `s` or raises KeyError. |
| `s.symmetric_difference(t)` | Items in either `s` or `t` but not both. |
| `s.symmetric_difference_update(t)` | Updates `s` to symmetric difference of `s` and `t`. |
| `s.union(t)`        | Returns union of `s` and `t`.          |
| `s.update(t)`       | Adds items from `t` to `s`.            |

---

## Table 10.9: String Operators and Methods

| Operation               | Description                                                         |
|-------------------------|---------------------------------------------------------------------|
| `s + t`                 | Concatenates `s` and `t` if `t` is a string.                       |
| `s * n`                 | Replicates string `s` `n` times.                                    |
| `s % x`                 | Formats string using tuple `x`.                                     |
| `s[i]`                  | Returns character at index `i`.                                     |
| `s[i:j]`                | Returns substring from index `i` to `j`.                           |
| `s[i:j:stride]`         | Returns substring with steps.                                       |
| `len(s)`                | Returns length of string `s`.                                       |
| `s.capitalize()`        | Capitalizes first character of `s`.                                 |
| `s.casefold()`          | Returns caseless version of `s` for comparisons.                    |
| `s.center(width [, pad])` | Centers `s` in field of `width`, padded by `pad`.               |
| `s.count(sub [, start [, end]])` | Counts occurrences of substring `sub`.                |
| `s.decode([encoding [, errors]])` | Decodes byte string into text (for bytes).         |
| `s.encode([encoding [, errors]])` | Encodes string using specified encoding.            |
| `s.endswith(suffix [, start [, end]])` | Checks if `s` ends with `suffix`.             |
| `s.expandtabs([tabsize])` | Replaces tabs in `s` with spaces.                                 |
| `s.find(sub [, start [, end]])` | Finds first index of `sub` in `s`.                      |
| `s.format(args, *kwargs)` | Formats string using positional and keyword arguments.            |
| `s.format_map(m)`        | Formats string using mapping `m`.                                   |
| `s.index(sub [, start [, end]])` | Like `find()` but raises error if not found.             |
| `s.isalnum()`            | Checks if all characters in `s` are alphanumeric.                  |
| `s.isalpha()`            | Checks if all characters in `s` are alphabetic.                    |
| `s.isascii()`            | Checks if all characters in `s` are ASCII.                       |
| `s.isdecimal()`          | Checks if all characters in `s` are decimal characters.            |
| `s.isdigit()`            | Checks if all characters in `s` are digits.                        |
| `s.isidentifier()`       | Checks if `s` is a valid identifier.                               |
| `s.islower()`            | Checks if all characters in `s` are lowercase.                     |
| `s.isnumeric()`          | Checks if all characters in `s` are numeric.                       |
| `s.isprintable()`        | Checks if all characters in `s` are printable.                     |
| `s.isspace()`            | Checks if all characters in `s` are whitespace.                    |
| `s.istitle()`            | Checks if `s` is title-cased.                                      |
| `s.isupper()`            | Checks if all characters in `s` are uppercase.                     |
| `s.join(t)`              | Joins sequence `t` with delimiter `s`.                             |
| `s.ljust(width [, fill])`| Left-justifies `s` in field of width `width`.                     |
| `s.lower()`              | Converts `s` to lowercase.                                         |
| `s.lstrip([chrs])`       | Removes leading characters specified in `chrs` from `s`.           |
| `s.maketrans(x [, y [, z]])` | Creates a translation table for substitutions.              |
| `s.partition(sep)`       | Splits `s` into tuple `(head, sep, tail)` at first occurrence of `sep`. |
| `s.removeprefix(prefix)` | Removes prefix `prefix` from `s` if present.                      |
| `s.removesuffix(suffix)` | Removes suffix `suffix` from `s` if present.                      |
| `s.replace(old, new [, maxreplace])` | Replaces occurrences of `old` with `new` in `s`.    |
| `s.rfind(sub [, start [, end]])` | Finds last index of `sub` in `s`.                    |
| `s.rindex(sub [, start [, end]])` | Like `rfind()` but raises error if not found.        |
| `s.rjust(width [, fill])`| Right-justifies `s` in field of width `width`.                    |
| `s.rpartition(sep)`      | Splits `s` into tuple `(head, sep, tail)` at last occurrence of `sep`. |
| `s.rsplit([sep [, maxsplit]])` | Splits `s` from right using separator `sep`.            |
| `s.rstrip([chrs])`       | Removes trailing characters specified in `chrs` from `s`.          |
| `s.split([sep [, maxsplit]])` | Splits `s` using separator `sep`.                     |
| `s.splitlines([keepends])` | Splits `s` into list of lines.                           |
| `s.startswith(prefix [, start [, end]])` | Checks if `s` starts with `prefix`.        |
| `s.strip([chrs])`        | Removes leading and trailing characters `chrs` from `s`.           |
| `s.swapcase()`           | Swaps case of letters in `s`.                                     |
| `s.title()`              | Converts `s` to title case.                                        |
| `s.translate(table [, deletechars])` | Translates characters in `s` using table.          |
| `s.upper()`              | Converts `s` to uppercase.                                         |
| `s.zfill(width)`         | Pads `s` on left with zeros to fill width.                        |

---

## Table 10.10: Tuple Operators and Methods

| Operation               | Description                                                           |
|-------------------------|-----------------------------------------------------------------------|
| `s + t`                 | Concatenates tuples if `t` is a tuple.                                |
| `s * n`                 | Replicates tuple `s` `n` times.                                       |
| `s[i]`                  | Returns element at index `i` of tuple `s`.                            |
| `s[i:j]`                | Returns a slice of tuple `s` from index `i` to `j`.                   |
| `s[i:j:stride]`         | Returns an extended slice of tuple `s`.                                |
| `len(s)`                | Number of elements in tuple `s`.                                      |
| `s.count(x)`            | Counts occurrences of element `x` in tuple `s`.                       |
| `s.index(x [, start [, stop]])` | Returns first index of `x` in `s` (optionally within [start, stop]). |

---

# Additional Built-in Python Libraries and Their Usage

The Python Standard Library offers a wealth of modules beyond built-in types and functions. Here is a brief table summarizing additional useful libraries, along with a short description of their usage:

| Module       | Description                                                               | Common Usage Examples                                      |
|--------------|---------------------------------------------------------------------------|------------------------------------------------------------|
| `collections`| Specialized container datatypes                                           | `deque`, `Counter`, `defaultdict`, `OrderedDict`            |
| `datetime`   | Basic date and time types                                                   | Creating date/time objects, arithmetic on dates            |
| `itertools`  | Functions creating iterators for efficient looping                          | `chain`, `cycle`, `product`, `permutations`, `combinations` |
| `inspect`    | Inspect live objects                                                          | Getting function signatures, source code introspection      |
| `math`       | Mathematical functions                                                      | `sqrt`, `sin`, `cos`, constants like `pi`, `e`             |
| `os`         | Miscellaneous operating system interfaces                                   | File/directory operations, environment variables           |
| `random`     | Generate pseudo-random numbers                                              | `random()`, `randint()`, shuffling sequences               |
| `re`         | Regular expressions                                                         | Pattern matching, search and replace text                 |
| `shutil`     | High-level file operations                                                  | Copying files/dirs, archiving directories                 |
| `statistics` | Mathematical statistics functions                                           | `mean`, `median`, `stdev`                                  |
| `sys`        | System-specific parameters and functions                                    | Command-line arguments (`sys.argv`), exit codes, stderr    |
| `time`       | Time access and conversions                                                 | `sleep()`, `time()`, performance counters                  |
| `turtle`     | Turtle graphics for simple drawing                                          | Educational graphics programming                           |
| `unittest`   | Unit testing framework                                                      | Writing and running tests, test discovery                 |

These modules extend Python's capabilities and are essential tools for many programming tasks.

# Comprehensive Tables for Python Standard Library Modules

Below are refined tables for several Python standard library modules, summarizing their primary classes, functions, and methods. For exhaustive details, refer to the official Python documentation for each module.

---

## `collections` Module: Main Classes and Methods

| Class/Function | Description              | Key Methods/Attributes                                                                                                                                       |
|----------------|--------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `deque`        | Double-ended queue       | `append()`, `appendleft()`, `pop()`, `popleft()`, `extend()`, `extendleft()`, `rotate()`, `clear()`, `index()`, `insert()`, `remove()`                    |
| `Counter`      | Counts hashable objects  | `elements()`, `most_common()`, `subtract()`, `update()`                                                                                                        |
| `defaultdict`  | Dict with default values | Inherits dict methods; uses a factory function for missing keys                                                                                                 |
| `OrderedDict`  | Dict with order memory   | All dict methods, plus `move_to_end()`, `popitem(last=True)`                                                                                                   |

---

## `datetime` Module: Key Classes and Methods

| Class      | Description                          | Key Methods/Attributes                                                                  |
|------------|--------------------------------------|-----------------------------------------------------------------------------------------|
| `date`     | Represents a date (year, month, day) | `today()`, `fromtimestamp()`, `isoformat()`, `weekday()`, `strftime()`                  |
| `time`     | Represents a time                    | `isoformat()`, `strftime()`, comparison operators                                       |
| `datetime` | Combines date and time               | `now()`, `utcnow()`, `fromtimestamp()`, `combine()`, `strftime()`, `date()`, `time()`, arithmetic |
| `timedelta`| Duration between dates/times         | Attributes: `days`, `seconds`, `microseconds`; supports arithmetic operations            |

---

## `itertools` Module: Main Functions

| Function                                | Description                            | Example Usage                                 |
|-----------------------------------------|----------------------------------------|-----------------------------------------------|
| `chain(*iterables)`                     | Concatenates multiple iterables        | `chain([1,2], [3,4])` -> 1,2,3,4              |
| `cycle(iterable)`                       | Repeats an iterable indefinitely       | `cycle('AB')` -> A, B, A, B, ...             |
| `product(*iterables, repeat=1)`         | Cartesian product of iterables         | `product([1,2], repeat=2)` -> (1,1), (1,2)... |
| `permutations(iterable, r=None)`        | All possible orderings of length r     | `permutations([1,2,3], 2)`                   |
| `combinations(iterable, r)`             | All possible r-length combinations     | `combinations('ABCD', 2)`                     |
| `combinations_with_replacement(iterable, r)` | Combinations with replacement    | `combinations_with_replacement('AB', 2)`      |

---

## `inspect` Module: Main Functions

| Function                              | Description                                                |
|---------------------------------------|------------------------------------------------------------|
| `getmembers(object, predicate=None)`  | Returns all members of an object, optionally filtered.     |
| `signature(callable)`                 | Returns a Signature object for the callable.               |
| `ismodule(obj)`, `isfunction(obj)`, `isclass(obj)` | Checks object type.                                |
| `getsource(object)`                   | Returns the source code of the object.                     |
| `getdoc(object)`                      | Returns the documentation string for the object.           |

---

## `math` Module: Key Functions and Constants

| Function/Constant       | Description                                |
|-------------------------|--------------------------------------------|
| `sqrt(x)`               | Square root of x.                          |
| `sin(x)`, `cos(x)`, `tan(x)` | Trigonometric functions.             |
| `log(x[, base])`        | Logarithm of x with specified base.        |
| `exp(x)`                | Exponential of x.                          |
| `pi`, `e`               | Mathematical constants π and e.            |
| `floor(x)`, `ceil(x)`   | Floor and ceiling functions.               |
| `gcd(a, b)`             | Greatest common divisor of a and b.        |

---

## `os` Module: Common Functions

| Function               | Description                                   |
|------------------------|-----------------------------------------------|
| `os.listdir(path)`     | Lists entries in directory `path`.            |
| `os.path.join(a, b)`   | Joins one or more path components.            |
| `os.getcwd()`          | Returns current working directory.            |
| `os.chdir(path)`       | Changes current working directory to `path`.  |
| `os.mkdir(path)`       | Creates a new directory named `path`.         |
| `os.remove(path)`      | Removes (deletes) the file `path`.            |
| `os.rename(src, dst)`  | Renames file or directory from `src` to `dst`.|
| `os.environ`           | Mapping of environment variables.             |

---

## `ipaddress` Module: Main Classes and Methods

| Class                 | Description                  | Key Methods/Attributes                                                   |
|-----------------------|------------------------------|--------------------------------------------------------------------------|
| `IPv4Address(address)`| Represents an IPv4 address   | `packed`, `exploded`, `compressed`, `is_private`, `is_global`            |
| `IPv6Address(address)`| Represents an IPv6 address   | `packed`, `exploded`, `compressed`, `is_private`, `is_link_local`        |
| `IPv4Network(network)`| Represents an IPv4 network   | `hosts()`, `network_address`, `broadcast_address`, `with_prefixlen`      |
| `IPv6Network(network)`| Represents an IPv6 network   | Similar to `IPv4Network` methods                                         |
| `ip_address(address)` | Factory for IP address objects | Returns IPv4Address or IPv6Address based on input                        |
| `ip_network(address, strict=True)` | Creates IP network object | Accepts CIDR notation; returns appropriate network object                 |

---

## `pathlib` Module: Main Classes and Methods

| Class | Description                 | Key Methods/Attributes                                                                   |
|-------|-----------------------------|------------------------------------------------------------------------------------------|
| `Path`| Represents filesystem paths | `cwd()`, `home()`, `exists()`, `iterdir()`, `mkdir()`, `open()`, `read_text()`, `write_text()`, `rglob()`, `joinpath()` |

---

## `socket` Module: Key Classes and Methods

| Class/Function                | Description                                                         |
|-------------------------------|---------------------------------------------------------------------|
| `socket.socket(family, type)` | Creates a new socket using specified address family and socket type.|
| `socket.connect(address)`     | Connects the socket to a remote address.                            |
| `socket.bind(address)`        | Binds the socket to a local address.                                |
| `socket.listen(backlog)`      | Enables a server to accept connections.                             |
| `socket.accept()`             | Accepts a connection, returning a new socket and address pair.      |
| `socket.send(bytes)`          | Sends data to the connected remote socket.                          |
| `socket.recv(bufsize)`        | Receives data from the socket.                                      |
| `socket.close()`              | Closes the socket.                                                  |

---

## `threading` Module: Key Classes and Methods

| Class/Function                | Description                                                         |
|-------------------------------|---------------------------------------------------------------------|
| `threading.Thread(target, args)` | Creates a new thread to run a target function with arguments.    |
| `thread.start()`              | Starts thread execution.                                            |
| `thread.join(timeout=None)`   | Waits for thread to finish, with optional timeout.                  |
| `threading.Lock()`            | Creates a lock object for thread synchronization.                   |
| `lock.acquire(blocking=True)` | Acquires the lock.                                                  |
| `lock.release()`              | Releases the lock.                                                  |
| `threading.Event()`           | Creates an event object for thread communication.                   |
| `event.set()`                 | Sets the event flag.                                                |
| `event.clear()`               | Clears the event flag.                                              |
| `event.wait(timeout=None)`    | Waits until the event is set or a timeout occurs.                   |
| `threading.Semaphore(value=1)`| Creates a semaphore to control access to a resource.                |

---

## `multiprocessing` Module: Key Classes and Methods

| Class/Function                           | Description                                                               |
|------------------------------------------|---------------------------------------------------------------------------|
| `multiprocessing.Process(target, args)` | Creates a new process to run a target function with arguments.            |
| `process.start()`                        | Starts process execution.                                                 |
| `process.join(timeout=None)`             | Waits for the process to complete, with optional timeout.                 |
| `multiprocessing.Lock()`                 | Creates a lock object for process synchronization.                        |
| `multiprocessing.Queue()`                | Creates a queue for inter-process communication.                          |
| `queue.put(item)`                        | Puts an item into the queue.                                              |
| `queue.get()`                            | Retrieves an item from the queue.                                         |
| `multiprocessing.Pool(processes)`        | Manages a pool of worker processes.                                       |
| `pool.map(func, iterable)`               | Applies a function to items in an iterable across processes.              |

---

## `asyncio` Module: Key Functions and Classes

| Class/Function                     | Description                                                             |
|------------------------------------|-------------------------------------------------------------------------|
| `asyncio.run(main())`              | Runs an async main function, managing the event loop lifecycle.         |
| `asyncio.create_task(coro)`        | Schedules the execution of a coroutine as a task.                       |
| `await asyncio.sleep(seconds)`     | Asynchronously waits for a specified time without blocking the event loop. |
| `asyncio.gather(*coros)`           | Runs multiple coroutines concurrently and aggregates results.             |
| `asyncio.Event()`                  | Creates an event for asyncio tasks communication.                       |
| `asyncio.Lock()`                   | Creates an asynchronous lock for coroutine synchronization.             |
| `asyncio.Queue()`                  | Creates an asynchronous queue for inter-task communication.             |
| `asyncio.open_connection(host, port)` | Opens a network connection, returning reader and writer streams.      |

*Note: These tables present key aspects and methods of each module. Each module offers many more features and functions. For comprehensive details, consult the official Python documentation.*
