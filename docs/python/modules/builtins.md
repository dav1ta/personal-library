# Builtins

## `abs(x)`
Returns the absolute value of `x`.

## `all(s)`
Returns `True` if all of the values in the iterable `s` evaluate as `True`. Returns `True` if `s` is empty.

## `any(s)`
Returns `True` if any of the values in the iterable `s` evaluate as `True`. Returns `False` if `s` is empty.

## `ascii(x)`
Creates a printable representation of the object `x` just like the `repr()`, but only uses ASCII characters in the result. Non-ASCII characters are turned into appropriate escape sequences. This can be used to view Unicode strings in a terminal or shell that doesn’t support Unicode.

## `bin(x)`
Returns a string with the binary representation of the integer `x`.

## `bool([x])`
Type representing Boolean values `True` and `False`. If used to convert `x`, it returns `True` if `x` evaluates to true using the usual truth-testing semantics—that is, nonzero number, nonempty list, and so on. Otherwise, `False` is returned. `False` is also the default value returned if `bool()` is called without any arguments. The `bool` class inherits from `int`, so the Boolean values `True` and `False` can be used as integers with values `1` and `0` in mathematical calculations.

## `breakpoint()`
Sets a manual debugger breakpoint. When encountered, control will transfer to `pdb`, the Python debugger.

## `bytearray([x])`
A type representing a mutable array of bytes. When creating an instance, `x` may either be an iterable sequence of integers in the range 0 to 255, an 8-bit string or bytes literal, or an integer that specifies the size of the byte array (in which case every entry will be initialized to 0).

## `bytearray(s, encoding)`
An alternative calling convention for creating a `bytearray` instance from characters in a string `s` where `encoding` specifies the character encoding to use in the conversion.

## `bytes([x])`
A type representing an immutable array of bytes.

## `bytes(s, encoding)`
An alternate calling convention for creating bytes from a string `s` where `encoding` specifies the encoding to use in conversion.

Table 10.1 shows operations supported by both bytes and byte arrays.

Table 10.1: Operations on Bytes and Bytearrays

| Operation              | Description                                            |
|------------------------|--------------------------------------------------------|
| `s + t`                | Concatenates if `t` is bytes.                           |
| `s * n`                | Replicates if `n` is an integer.                        |
| `s % x`                | Formats bytes. `x` is tuple.                            |
| `s[i]`                 | Returns element `i` as an integer.                      |
| `s[i:j]`               | Returns a slice.                                       |
| `s[i:j:stride]`        | Returns an extended slice.                              |
| `len(s)`               | Number of bytes in `s`.                                |
| `s.capitalize()`       | Capitalizes the first character.                        |
| `s.center(width [, pad])` | Centers the string in a field of length `width`. `pad` is a padding character. |
| `s.count(sub [, start [, end]])` | Counts occurrences of the specified substring `sub`. |
| `s.decode([encoding [, errors]])` | Decodes a byte string into text (bytes type only).   |
| `s.endswith(suffix [, start [, end]])` | Checks the end of the string for a suffix.       |
| `s.expandtabs([tabsize])` | Replaces tabs with spaces.                         |
| `s.find(sub [, start [, end]])` | Finds the first occurrence of the specified substring `sub`. |
| `s.hex()`              | Converts to a hexadecimal string.                      |
| `s.index(sub [, start [, end]])` | Finds the first occurrence or error in the specified substring `sub`. |
| `s.isalnum()`          | Checks whether all characters are alphanumeric.        |
| `s.isalpha()`          | Checks whether all characters are alphabetic.          |
| `s.isascii()`          | Checks whether all characters are ASCII.               |
| `s.isdigit()`          | Checks whether all characters are digits.              |
| `s.islower()`          | Checks whether all characters are lowercase.           |
| `s.isspace()`          | Checks whether all characters are whitespace.          |
| `s.istitle()`          | Checks whether the string is a title-cased string (first letter of each word capitalized). |
| `s.isupper()`          | Checks whether all characters are uppercase.           |
| `s.join(t)`            | Joins a sequence of strings `t` using a delimiter `s`. |
| `s.ljust(width [, fill])` | Left-aligns `s` in a string of size `width`. `fill` is a padding character. |
| `s.lower()`            | Converts to lowercase.                                  |
| `s.lstrip([chrs])`     | Removes leading whitespace or characters supplied in `chrs`. |
| `s.maketrans(x [, y [, z]])` | Makes a translation table for `s.translate()`.      |
| `s.partition(sep)`    | Partitions a string based on a separator string `sep`. Returns a tuple `(head, sep, tail)` or `(s, '', '')` if `sep` isn’t found. |
| `s.removeprefix(prefix)` | Returns `s` with a given prefix removed if present.  |
| `s.removesuffix(suffix)` | Returns `s` with a given suffix removed if present.  |
| `s.replace(old, new [, maxreplace])` | Replaces a substring.                          |
| `s.rfind(sub [, start [, end]])` | Finds the last occurrence of a substring.            |
| `s.rindex(sub [, start [, end]])` | Finds the last occurrence or raises an error.        |
| `s.rjust(width [, fill])` | Right-aligns `s` in a string of length `width`. `fill` is a padding character. |
| `s.rpartition(sep)`   | Partitions `s` based on a separator `sep`, but searches from the end of the string. |
| `s.rsplit([sep [, maxsplit]])` | Splits a string from the end of the string using `sep` as a delimiter. `maxsplit` is the maximum number of splits to perform. If `maxsplit` is omitted, the result is identical to the `split()` method. |
| `s.rstrip([chrs])`    | Removes trailing whitespace or characters supplied in `chrs`. |
| `s.split([sep [, maxsplit]])` | Splits a string using `sep` as a delimiter. `maxsplit` is the maximum number of splits to perform. |
| `s.splitlines([keepends])` | Splits a string into a list of lines. If `keepends` is `1`, trailing newlines are preserved. |
| `s.startswith(prefix [, start [, end]])` | Checks whether a string starts with `prefix`.       |
| `s.strip([chrs])`     | Removes leading and trailing whitespace or characters supplied in `chrs`. |
| `s.swapcase()`        | Converts uppercase to lowercase, and vice versa.       |
| `s.title()`           | Returns a title-cased version of the string.            |
| `s.translate(table [, deletechars])` | Translates a string using a character translation table `table`, removing characters in `deletechars`. |
| `s.upper()`           | Converts a string to uppercase.                         |
| `s.zfill(width)`      | Pads a string with zeros on the left up to the specified `width`. |

Byte arrays additionally support the methods in Table 10.2.

Table 10.2: Additional Operations on Byte Arrays

| Operation              | Description                                           |
|------------------------|-------------------------------------------------------|
| `s[i] = v`             | Item assignment.                                      |
| `s[i:j] = t`           | Slice assignment.                                     |
| `s[i:j:stride] = t`    | Extended slice assignment.                            |
| `del s[i]`             | Item deletion.                                        |
| `del s[i:j]`           | Slice deletion.                                       |
| `del s[i:j:stride]`    | Extended slice deletion.                              |
| `s.append(x)`          | Appends a new byte to the end.                         |
| `s.clear()`            | Clears the byte array.                                |
| `s.copy()`             | Makes a copy.                                         |
| `s.extend(t)`          | Extends `s` with bytes from `t`.                       |
| `s.insert(n, x)`       | Inserts byte `x` at index `n`.                         |
| `s.pop([n])`           | Removes and returns byte at index `n`.                 |
| `s.remove(x)`          | Removes first occurrence of byte `x`.                  |
| `s.reverse()`          | Reverses the byte array in-place.                      |

## `callable(obj)`
Returns `True` if `obj` is callable as a function.

## `chr(x)`
Converts the integer `x` representing a Unicode code-point into a single-character string.


## callable(obj)
Returns True if `obj` is callable as a function.

## chr(x)
Converts the integer `x` representing a Unicode code-point into a single-character string.

## classmethod(func)
This decorator creates a class method for the function `func`. It is typically only used inside class definitions where it is implicitly invoked using `@classmethod`. Unlike a normal method, a class method receives the class as the first argument, not an instance.

## compile(string, filename, kind)
Compiles `string` into a code object for use with `exec()` or `eval()`. `string` is a string containing valid Python code. If this code spans multiple lines, the lines must be terminated by a single newline ('\n') and not platform-specific variants (for example, '\r\n' on Windows). `filename` is a string containing the name of the file in which the string was defined (if any). `kind` is 'exec' for a sequence of statements, 'eval' for a single expression, or 'single' for a single executable statement. The resulting code object that is returned can be directly passed to `exec()` or `eval()` in place of a string.

## complex([real [, imag]])
Type representing a complex number with real and imaginary components, `real` and `imag`, which can be supplied as any numeric type. If `imag` is omitted, the imaginary component is set to zero. If `real` is passed as a string, the string is parsed and converted to a complex number. In this case, `imag` should be omitted. If `real` is any other kind of object, the value of `real.complex()` is returned. If no arguments are given, `0j` is returned.

Table 10.3: Attributes of complex

| Attribute/Method  | Description                    |
|-------------------|--------------------------------|
| `z.real`          | Real component                 |
| `z.imag`          | Imaginary component            |
| `z.conjugate()`   | Conjugates as a complex number |

Click [here](#code-image) to view code image.

## delattr(object, attr)
Deletes an attribute of an object. `attr` is a string. Same as `del object.attr`.

Click [here](#code-image) to view code image.

## dict([m]) or dict(key1=value1, key2=value2, ...)
Type representing a dictionary. If no argument is given, an empty dictionary is returned. If `m` is a mapping object (such as another dictionary), a new dictionary having the same keys and same values as `m` is returned. For example, if `m` is a dictionary, `dict(m)` makes a shallow copy of it. If `m` is not a mapping, it must support iteration in which a sequence of `(key, value)` pairs is produced. These pairs are used to populate the dictionary. `dict()` can also be called with keyword arguments. For example, `dict(foo=3, bar=7)` creates the dictionary `{'foo': 3, 'bar': 7}`.

Table 10.4: Operations on Dictionaries

| Operation       | Description                             |
|-----------------|-----------------------------------------|
| `m | n`         | Merges `m` and `n` into a single dictionary. |
| `len(m)`        | Returns the number of items in `m`.      |
| `m[k]`          | Returns the item of `m` with key `k`.    |
| `m[k]=x`        | Sets `m[k]` to `x`.                      |
| `del m[k]`      | Removes `m[k]` from `m`.                 |
| `k in m`        | Returns `True` if `k` is a key in `m`.   |
| `m.clear()`     | Removes all items from `m`.              |
| `m.copy()`      | Makes a shallow copy of `m`.             |
| `m.fromkeys(s [, value])` | Creates a new dictionary with keys from sequence `s` and values all set to `value`. |
| `m.get(k [, v])` | Returns `m[k]` if found; otherwise, returns `v`. |
| `m.items()`     | Returns `(key, value)` pairs.            |
| `m.keys()`      | Returns the keys.                        |
| `m.pop(k [, default])` | Returns `m[k]` if found and removes it from `m`; otherwise, returns `default` if supplied or raises `KeyError` if not. |
| `m.popitem()`   | Removes a random `(key, value)` pair from `m` and returns it as a tuple. |
| `m.setdefault(k [, v])` | Returns `m[k]` if found; otherwise, returns `v` and sets `m[k] = v`. |
| `m.update(b)`   | Adds all objects from `b` to `m`.        |
| `m.values()`    | Returns the values.                      |

## dir([object])
Returns a sorted list of attribute names. If `object` is a module, it contains the list of symbols defined in that module. If `object` is a type or class object, it returns a list of attribute names. The names are typically obtained from the object’s `dict` attribute if defined, but other sources may be used. If no argument is given, the names in the current local symbol table are returned. It should be noted that this function is primarily used for informational purposes (for example, used interactively at the command line). It should not be used for formal program analysis because the information obtained may be incomplete. Also, user-defined classes can define a special method `dir()` that alters the result of this function.

## divmod(a, b)
Returns the quotient and remainder of long division as a tuple. For integers, the value `(a // b, a % b)` is returned. For floats, `(math.floor(a / b), a % b)` is returned. This function may not be called with complex numbers.

Click [here](#code-image) to view code image.

## enumerate(iter, start=0)
Given an iterable object, `iter`, returns a new iterator (of type `enumerate`) that produces tuples containing a count and the value produced from `iter`. For example, if `iter` produces `a, b, c`, then `enumerate(iter)` produces `(0,a)`, `(1,b)`, `(2,c)`. The optional `start` changes the initial value of the count.

Click [here](#code-image) to view code image.

## eval(expr [, globals [, locals]])
Evaluates an expression. `expr` is a string or a code object created by `compile()`. `globals` and `locals` are mapping objects that define the global and local namespaces, respectively, for the operation. If omitted, the expression is evaluated using the values of `globals()` and `locals()` as executed in the caller’s environment. It is most common for `globals` and `locals` to be specified as dictionaries, but advanced applications can supply custom mapping objects.

Click [here](#code-image) to view code image.

## exec(code [, global [, locals]])
Executes Python statements. `code` is a string, bytes, or a code object created by `compile()`. `globals` and `locals` define the global and local namespaces, respectively, for the operation. If omitted, the code is executed using the values of `globals()` and `locals()` as executed in the caller’s environment.

Click [here](#code-image) to view code image.

## filter(function, iterable)
Creates an iterator that returns the items in `iterable` for which `function(item)` evaluates as `True`.

## float([x])
Type representing a floating-point number. If `x` is a number, it is converted to a float. If `x` is a string, it is parsed into a float. For all other objects, `x.float()` is invoked. If no argument is supplied, `0.0` is returned.

Table 10.5: Methods and Attributes of Floats

| Attribute/Method   | Description                       |
|--------------------|-----------------------------------|
| `x.real`           | Real component when used as a complex. |
| `x.imag`           | Imaginary component when used as a complex. |
| `x.conjugate()`    | Conjugates as a complex number.    |
| `x.as_integer_ratio()` | Converts to numerator/denominator pair. |
| `x.hex()`          | Creates a hexadecimal representation. |
| `x.is_integer()`   | Tests if an exact integer value.   |
| `float.fromhex(s)` | Creates from a hexadecimal string. A class method. |


## Python Built-in Functions

### `bytes([source[, encoding[, errors]]])`

Constructs a new `bytes` object. The `source` parameter can be used to initialize the `bytes` object from a sequence of integers or another object that implements the buffer protocol. If `source` is specified, the encoding and errors parameters must not be specified.

### `callable(obj)`

Returns `True` if `obj` is callable as a function.

### `chr(x)`

Converts the integer `x` representing a Unicode code-point into a single-character string.

### `classmethod(func)`

This decorator creates a class method for the function `func`. It is typically only used inside class definitions where it is implicitly invoked using `@classmethod`. Unlike a normal method, a class method receives the class as the first argument, not an instance.

### `compile(string, filename, kind)`

Compiles `string` into a code object for use with `exec()` or `eval()`. `string` is a string containing valid Python code. If this code spans multiple lines, the lines must be terminated by a single newline (`'\n'`) and not platform-specific variants (for example, `'\r\n'` on Windows). `filename` is a string containing the name of the file in which the string was defined (if any). `kind` is `'exec'` for a sequence of statements, `'eval'` for a single expression, or `'single'` for a single executable statement. The resulting code object that is returned can be directly passed to `exec()` or `eval()` in place of a string.

### `complex([real [, imag]])`

Type representing a complex number with real and imaginary components, `real` and `imag`, which can be supplied as any numeric type. If `imag` is omitted, the imaginary component is set to zero. If `real` is passed as a string, the string is parsed and converted to a complex number. In this case, `imag` should be omitted. If `real` is any other kind of object, the value of `real.complex()` is returned. If no arguments are given, `0j` is returned.

### `delattr(object, attr)`

Deletes an attribute of an object. `attr` is a string. Same as `del object.attr`.

### `dict([m])` or `dict(key1=value1, key2=value2, ...)`

Type representing a dictionary. If no argument is given, an empty dictionary is returned. If `m` is a mapping object (such as another dictionary), a new dictionary having the same keys and same values as `m` is returned. For example, if `m` is a dictionary, `dict(m)` makes a shallow copy of it. If `m` is not a mapping, it must support iteration in which a sequence of `(key, value)` pairs is produced. These pairs are used to populate the dictionary. `dict()` can also be called with keyword arguments. For example, `dict(foo=3, bar=7)` creates the dictionary `{'foo': 3, 'bar': 7}`.

### `dir([object])`

Returns a sorted list of attribute names. If `object` is a module, it contains the list of symbols defined in that module. If `object` is a type or class object, it returns a list of attribute names. The names are typically obtained from the object’s `dict` attribute if defined, but other sources may be used. If no argument is given, the names in the current local symbol table are returned. It should be noted that this function is primarily used for informational purposes (for example, used interactively at a Python prompt).

### `divmod(x, y)`

Returns a pair of numbers `(q, r)` such that `x = y * q + r`. If `x` and `y` are integers, the return value is also an integer. For example, `divmod(7, 3)` returns `(2, 1)`, where `2` is the quotient and `1` is the remainder.

### `enumerate(iterable[, start])`

Returns an iterator that generates pairs consisting of an index and an item from the `iterable`. The `start` parameter is an optional integer that specifies the starting value of the index. By default, it is `0`.

### `eval(expression[, globals[, locals]])`

Evaluates the `expression` in the given `globals` and `locals` namespaces. `expression` can be a string or a code object. If `globals` is specified, it must be a dictionary. If `locals` is specified, it can be any mapping object. If both `globals` and `locals` are omitted, the expression is evaluated in the context of the current global and local namespaces.

### `exec(object[, globals[, locals]])`

Evaluates the `object` as a Python expression or statement. `object` can be a string or a code object. If `globals` is specified, it must be a dictionary. If `locals` is specified, it can be any mapping object. If both `globals` and `locals` are omitted, the code is executed in the context of the current global and local namespaces.

### `filter(function, iterable)`

Creates an iterator that produces the values from `iterable` for which `function` returns `True`. If `function` is `None`, the `identity` function is assumed, which returns `True` for all elements of the iterable. If `iterable` is a string, the resulting iterator produces the individual characters of the string.

### `float([x])`

Type representing a floating-point number. If no argument is given, `0.0` is returned. If `x` is a number, it is converted to a floating-point number. If `x` is a string, it is parsed and converted to a floating-point number.

### `format(value[, format_spec])`

Converts `value` to a formatted string according to the format specification string in `format_spec`. This operation invokes `value.format()`, which is free to interpret the format specification as it sees fit. For simple types of data, the format specifier typically includes an alignment character of `<`, `>`, or `^`, a number (which indicates the field width), and a character code of `d`, `f`, or `s` for integer, floating point, or string values, respectively. For example, a format specification of `'d'` formats an integer, a specification of `'8d'` right-aligns an integer in an 8-character field, and `'<8d'` left-aligns an integer in an 8-character field. More details on `format()` and format specifiers can be found in Chapter 9.

### `frozenset([iterable])`

Type representing an immutable set object populated with values taken from `iterable`. The values must also be immutable. If no argument is given, an empty set is returned. A `frozenset` supports all of the operations found on sets except for any operations that mutate a set in-place.

### `getattr(object, name[, default])`

Returns the value of a named attribute of an object. `name` is a string containing the attribute name. `default` is an optional value to return if no such attribute exists;

Table 10.8 shows operations on sets.

Table 10.8 Set Operations and Methods

Operation

Description

s | t

Union

s & t

Intersection

s - t

Difference

s ^ t

Symmetric difference

len(s)

Returns number of items in s.

s.add(item)

Adds item to s. Has no effect if item is already in s.

s.clear()

Removes all items from s.

s.copy()

Makes a copy of s.

s.difference(t)

Set difference. Returns all the items in s, but not in t.

s.difference_update(t)

Removes all the items from s that are also in t.

s.discard(item)

Removes item from s. If item is not a member of s, nothing happens.

s.intersection(t)

Intersection. Returns all the items that are both in s and in t.

s.intersection_update(t)

Computes the intersection of s and t and leaves the result in s.

s.isdisjoint(t)

Returns True if s and t have no items in common.

s.issubset(t)

Returns True if s is a subset of t.

s.issuperset(t)

Returns True if s is a superset of t.

s.pop()

Returns an arbitrary set element and removes it from s.

s.remove(item)

Removes item from s. If item is not a member, KeyError is raised.

s.symmetric_difference(t)

Symmetric difference. Returns all the items that are in s or t, but not in both sets.

s.symmetric_difference_update(t)

Computes the symmetric difference of s and t and leaves the result in s.

s.union(t)

Union. Returns all items in s or t.

s.update(t)

Adds all the items in t to s. t may be another set, a sequence, or any object that supports iteration.

Click here to view code image

setattr(object, name, value) Sets an attribute of an object. name is a string. Same as object.name = value.

Click here to view code image

slice([start,] stop [, step]) Returns a slice object representing integers in the specified range. Slice objects are also generated by the extended slice syntax a[i:i:k].

Click here to view code image

sorted(iterable, *, key=keyfunc, reverse=reverseflag) Creates a sorted list from items in iterable. The keyword argument key is a single-argument function that transforms values before they are compared. The keyword argument reverse is a Boolean flag that specifies whether or not the resulting list is sorted in reverse order. The key and reverse arguments must be specified using keywords—for example, sorted(a, key=get_name).

staticmethod(func) Creates a static method for use in classes. This function is usually used as a @staticmethod decorator.

str([object]) Type representing a string. If object is supplied, a string representation of its value is created by calling its str() method. This is the same string that you see when you print the object. If no argument is given, an empty string is created.

Table 10.9 shows methods defined on strings.

Table 10.9 String Operators and Methods

Operation

Description

s + t

Concatenates strings if t is a string.

s * n

Replicates a string if n is an integer.

s % x

Formats a string. x is tuple.

s[i]

Returns element i of a string.

s[i:j]

Returns a slice.

s[i:j:stride]

Returns an extended slice.

len(s)

Number of elements in s.

s.capitalize()

Capitalizes the first character.

s.casefold()

Converts s to a string usable for a caseless comparison.

s.center(width [, pad])

Centers the string in a field of length width. pad is a padding character.

s.count(sub [, start [, end]])

Counts occurrences of the specified substring sub.

s.decode([encoding [, errors]])

Decodes a byte string into text (bytes type only).

s.encode([encoding [, errors]])

Returns an encoded version of the string (str type only).

s.endswith(suffix [, start [, end]])

Checks the end of the string for a suffix.

s.expandtabs([tabsize])

Replaces tabs with spaces.

s.find(sub [, start [, end]])

Finds the first occurrence of the specified substring sub.

s.format(args, *kwargs)

Formats s (str type only).

s.format_map(m)

Formats s with substitutions taking from the mapping m (str type only).

s.index(sub [, start [, end]])

Finds the first occurrence or error in the specified substring sub.

s.isalnum()

Checks whether all characters are alphanumeric.

s.isalpha()

Checks whether all characters are alphabetic.

s.isascii()

Checks whether all characters are ASCII.

s.isdecimal()

Checks whether all characters are decimal characters. Does not match superscript, subscripts, or other special digits.

s.isdigit()

Checks whether all characters are digits. Matches superscripts and superscripts, but not vulgar fractions.

s.isidentifier()

Checks whether s is a valid Python identifier.

s.islower()

Checks whether all characters are lowercase.

s.isnumeric()

Checks whether all characters are numeric. Matches all forms of numeric characters such as vulgar fractions, Roman numerals, etc.

s.isprintable()

Checks whether all characters are printable.

s.isspace()

Checks whether all characters are whitespace.

s.istitle()

Checks whether the string is a title-cased string (first letter of each word capitalized).

s.isupper()

Checks whether all characters are uppercase.

s.join(t)

Joins a sequence of strings t using a delimiter s.

s.ljust(width [, fill])

Left-aligns s in a string of size width.

s.lower()

Converts to lowercase.

s.lstrip([chrs])

Removes leading whitespace or characters supplied in chrs.

s.maketrans(x [, y [, z]])

Makes a translation table for s.translate().

s.partition(sep)

Partitions a string based on a separator string sep. Returns a tuple (head, sep, tail) or (s, '', '') if sep isn’t found.

s.removeprefix(prefix)

Returns s with a given prefix removed if present.

s.removesuffix(suffix)

Returns s with a given suffix removed if present.

s.replace(old, new [, maxreplace])

Replaces a substring.

s.rfind(sub [, start [, end]])

Finds the last occurrence of a substring.

s.rindex(sub [, start [, end]])

Finds the last occurrence or raises an error.

s.rjust(width [, fill])

Right-aligns s in a string of length width.

s.rpartition(sep)

Partitions s based on a separator sep, but searches from the end of the string.

s.rsplit([sep [, maxsplit]])

Splits a string from the end of the string using sep as a delimiter. maxsplit is the maximum number of splits to perform. If maxsplit is omitted, the result is identical to the split() method.

s.rstrip([chrs])

Removes trailing whitespace or characters supplied in chrs.

s.split([sep [, maxsplit]])

Splits a string using sep as a delimiter. maxsplit is the maximum number of splits to perform.

s.splitlines([keepends])

Splits a string into a list of lines. If keepends is 1, trailing newlines are preserved.

s.startswith(prefix [, start [, end]])

Checks whether a string starts with a prefix.

s.strip([chrs])

Removes leading and trailing whitespace or characters supplied in chrs.

s.swapcase()

Converts uppercase to lowercase, and vice versa.

s.title()

Returns a title-cased version of the string.

s.translate(table [, deletechars])

Translates a string using a character translation table table, removing characters in deletechars.

s.upper()

Converts a string to uppercase.

s.zfill(width)

Pads a string with zeros on the left up to the specified width.

sum(items [,initial]) Computes the sum of a sequence of items taken from the iterable object items. initial provides the starting value and defaults to 0. This function usually only works with numbers.

super() Returns an object that represents the collective superclasses of the class in which its used. The primary purpose of this object is to invoke methods in base classes. Here’s an example:

Click here to view code image

class B(A): def foo(self): super().foo() # Invoke foo() defined by superclasses.

tuple([items]) Type representing a tuple. If supplied, items is an iterable object that is used to populate the tuple. However, if items is already a tuple, it’s returned unmodified. If no argument is given, an empty tuple is returned.

Table 10.10 shows methods defined on tuples.

Table 10.10 Tuple Operators and Methods

Operation

Description

s + t

Concatenation if t is a list.

s * n

Replication if n is an integer.

s[i]

Returns element i of a s.

s[i:j]

Returns a slice.

s[i:j:stride]

Returns an extended slice.

len(s)

Number of elements in s.

s.append(x)

Appends a new element, x, to the end of s.

s.count(x)

Counts occurrences of x in s.

s.index(x [, start [, stop]])

Returns the smallest i where s[i] == x. start and stop optionally specify the starting and ending index for the search.

type(object) The base class of all types in Python. When called as a function, returns the type of object. This type is the same as the object’s class. For common types such as integers, floats, and lists, the type will refer to one of the other built-in classes such as int, float, list, and so forth. For user-defined objects, the type is the associated class. For objects related to Python’s internals, you will typically get a reference to one of the classes defined in the types module.

vars([object]) Returns the symbol table of object (usually found in its dict attribute). If no argument is given, a dictionary corresponding to the local namespace is returned. The dictionary returned by this function should be assumed to be read-only. It’s not safe to modify its contents.

zip([s1 [, s2 [, ... ]]]) Creates an iterator that produces tuples containing one item each from s1, s2, and so on. The nth tuple is (s1[n], s2[n], ... ). The resulting iterator stops when the shortest input is exhausted. If no arguments are given, the iterator produces no values.

10.2 Built-in Exceptions This section describes the built-in exceptions used to report different kinds of errors.

10.2.1 Exception Base Classes The following exceptions serve as base classes for all the other exceptions:

BaseException The root class for all exceptions. All built-in exceptions are derived from this class.

Exception The base class for all program-related exceptions. That includes all built-in exceptions except for SystemExit, GeneratorExit, and KeyboardInterrupt. User-defined exceptions should inherit from Exception.

ArithmeticError The base class for arithmetic exceptions, including OverflowError, ZeroDivisionError, and FloatingPointError.

LookupError The base class for indexing and key errors, including IndexError and KeyError.

EnvironmentError The base class for errors that occur outside Python. Is a synonym for OSError.

The preceding exceptions are never raised explicitly. However, they can be used to catch certain classes of errors. For instance, the following code would catch any sort of numerical error:

Click here to view code image

try: # Some operation ... except ArithmeticError as e: # Math error 10.2.2 Exception Attributes Instances of an exception e have a few standard attributes that can be useful to inspect and/or manipulate it in certain applications.

e.args The tuple of arguments supplied when raising the exception. In most cases, this is a one-item tuple with a string describing the error. For EnvironmentError exceptions, the value is a 2-tuple or 3-tuple containing an integer error number, string error message, and an optional filename. The contents of this tuple might be useful if you need to recreate the exception in a different context—for example, to raise an exception in a different Python interpreter process.

e.cause Previous exception when using explicit chained exceptions.

e.context Previous exception for implicitly chained exceptions.

e.traceback Traceback object associated with the exception.

10.2.3 Predefined Exception Classes The following exceptions are raised by programs:

AssertionError Failed assert statement.

AttributeError Failed attribute reference or assignment.

BufferError Memory buffer expected.

EOFError End of file. Generated by the built-in functions input() and raw_input(). It should be noted that most other I/O operations such as the read() and readline() methods of files return an empty string to signal EOF instead of raising an exception.

FloatingPointError Failed floating-point operation. It should be noted that floating-point exception handling is a tricky problem and this exception only gets raised if Python has been configured and built in a way that enables it. It is more common for floating-point errors to silently produce results such as float('nan') or float('inf'). A subclass of ArithmeticError.

GeneratorExit Raised inside a generator function to signal termination. This happens when a generator is destroyed prematurely (before all generator values are consumed) or the close() method of a generator is called. If a generator ignores this exception, the generator is terminated, and the exception is silently ignored.

IOError Failed I/O operation. The value is an IOError instance with the attributes errno, strerror, and filename. errno is an integer error number, strerror is a string error message, and filename is an optional filename. A subclass of EnvironmentError.

ImportError Raised when an import statement can’t find a module or when from can’t find a name in a module.

IndentationError Indentation error. A subclass of SyntaxError.

IndexError Sequence subscript out of range. A subclass of LookupError.

KeyError Key not found in a mapping. A subclass of LookupError.

KeyboardInterrupt Raised when the user hits the interrupt key (usually Ctrl+C).

MemoryError Recoverable out-of-memory error.

ModuleNotFoundError Module can’t be found by the import statement.

NameError Name not found in local or global namespaces.

NotImplementedError Unimplemented feature. Can be raised by base classes that require derived classes to implement certain methods. A subclass of RuntimeError.

OSError Operating system error. Primarily raised by functions in the os module. The following exceptions are subclasses: BlockingIOError, BrokenPipeError, ChildProcessError, ConnectionAbortedError, ConnectionError, ConnectionRefusedError, ConnectionResetError, FileExistsError, FileNotFoundError, InterruptedError, IsADirectoryError, NotADirectoryError, PermissionError, ProcessLookupError, TimeoutError.

OverflowError Result of an integer value being too large to be represented. This exception usually only arises if large integer values are passed to objects that internally rely upon fixed-precision machine integers in their implementation. For example, this error can arise with range or xrange objects if you specify starting or ending values that exceed 32 bits in size. A subclass of ArithmeticError.

RecursionError Recursion limit exceeded.

ReferenceError Result of accessing a weak reference after the underlying object has been destroyed (see the weakref module).

RuntimeError A generic error not covered by any of the other categories.

StopIteration Raised to signal the end of iteration. This normally happens in the next() method of an object or in a generator function.

StopAsyncIteration Raised to signal the end of asynchronous iteration. Only applicable in the context of async functions and generators.

SyntaxError Parser syntax error. Instances have the attributes filename, lineno, offset, and text, which can be used to gather more information.

SystemError Internal error in the interpreter. The value is a string indicating the problem.

SystemExit Raised by the sys.exit() function. The value is an integer indicating the return code. If it’s necessary to exit immediately, os._exit() can be used.

TabError Inconsistent tab usage. Generated when Python is run with the -tt option. A subclass of SyntaxError.

TypeError Occurs when an operation or function is applied to an object of an inappropriate type.

UnboundLocalError Unbound local variable referenced. This error occurs if a variable is referenced before it’s defined in a function. A subclass of NameError.

UnicodeError Unicode encoding or decoding error. A subclass of ValueError. The following exceptions are subclasses: UnicodeEncodeError, UnicodeDecodeError, UnicodeTranslateError.

ValueError Generated when the argument to a function or operation is the right type but an inappropriate value.

WindowsError Generated by failed system calls on Windows. A subclass of OSError.

ZeroDivisionError Dividing by zero. A subclass of ArithmeticError.

10.3 Standard Library Python comes with a sizable standard library. Many of these modules have been previously described in the book. Reference material can be found at https://docs.python.org/library. That material is not repeated here.

The modules listed below are notable because they are generally useful for a wide variety of applications and for Python programming in general.

10.3.1 collections Module The collections module supplements Python with a variety of additional container objects that can be quite useful for working with data—such as a double-ended queue (deque), dictionaries that automatically initialize missing items (defaultdict), and counters for tabulation (Counter).

10.3.2 datetime Module The datetime module is where you find functions related to dates, times, and computations involving those things.

10.3.3 itertools Module The itertools module provides a variety of useful iteration patterns—chaining iterables together, iterating over product sets, permutations, grouping, and similar operations.

10.3.4 inspect Module The inspect module provides functions for inspecting the internals of code-related elements such as functions, classes, generators, and coroutines. It’s commonly used in metaprogramming by functions that define decorators and similar features.

10.3.5 math Module The math module provides common mathematical functions such as sqrt(), cos(), and sin().

10.3.6 os Module The os module is where you find low-level functions related to the host operating system—processes, files, pipes, permissions, and similar features.

10.3.7 random Module The random module provides various functions related to random number generation.

10.3.8 re Module The re module provides support for working with text via regular expression pattern matching.

10.3.9 shutil Module The shutil module has functions for performing common tasks related to the shell, such as copying files and directories.

10.3.10 statistics Module The statistics module provides functions for computing common statistical values such as means, medians, and standard deviation.

10.3.11 sys Module The sys module contains a variety of attributes and methods related to the runtime environment of Python itself. This includes command-line options, standard I/O streams, the import path, and similar features.

10.3.12 time Module The time module is where you find various functions related to system time, such as getting the value of the system clock, sleeping, and the number of elapsed CPU seconds.

10.3.13 turtle Module Turtle graphics. You know, for kids.

10.3.14 unittest Module The unittest module provides built-in support for writing unit tests. Python itself is tested using unittest. However, many programmers prefer using third-party libraries such as pytest for testing. This author concurs.
