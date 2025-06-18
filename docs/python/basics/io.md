# Python I/O Operations

## Introduction

Python provides powerful tools for handling input and output operations. This guide covers various aspects of I/O in Python, including file operations, data representation, text encoding, and command-line arguments.

## Data Representation

### Bytes and Bytearrays
```python
# Bytes literals
a = b'hello'
b = bytes([0x68, 0x65, 0x6c, 0x6c, 0x6f])

# Bytearray operations
c = bytearray()
c.extend(b'world')   # c = 'world'
c.append(0x21)       # c = 'world!'

# Accessing bytes
print(a[0])     # prints 104
for x in b:     # Outputs 104 101 108 108 111
    print(x)

# Type differences
a = b'hello'     # bytes
b = 'hello'      # text
c = 'world'      # text

print(a == b)    # False
d = a + c        # TypeError: can't concat str to bytes
e = b + c        # 'helloworld' (both are strings)
```

## Text Encoding and Decoding

### Basic Encoding/Decoding
```python
# Text to bytes
text = 'hello'
bytes_data = text.encode('utf-8')

# Bytes to text
bytes_data = b'world'
text = bytes_data.decode('utf-8')
```

### Common Encodings
- `ascii`: Character values in range [0x00, 0x7f]
- `latin1`: Character values in range [0x00, 0xff]
- `utf-8`: Variable-length Unicode encoding
- `cp1252`: Common Windows encoding
- `macroman`: Common Macintosh encoding

## String Formatting

### Basic Formatting
```python
# Format method
x = 123.456
format(x, '0.2f')       # '123.46'
format(x, '10.4f')      # '  123.4560'
format(x, '<*10.2f')    # '123.46****'

# String alignment
name = 'Elwood'
r = format(name, '<10')     # 'Elwood    '
r = format(name, '>10')     # '    Elwood'
r = format(name, '^10')     # '  Elwood  '
r = format(name, '*^10')    # '**Elwood**'
```

### Format Specifiers
- `d`: Decimal integer
- `b`: Binary integer
- `o`: Octal integer
- `x`: Hexadecimal integer
- `f, F`: Floating point
- `e, E`: Scientific notation
- `g, G`: General format
- `n`: Locale-aware format
- `%`: Percentage
- `s`: String
- `c`: Character

### Format Examples
```python
# Integer formatting
x = 42
format(x, '10d')        # '        42'
format(x, '10x')        # '        2a'
format(x, '10b')        # '    101010'
format(x, '010b')       # '0000101010'

# Float formatting
y = 3.1415926
format(y, '10.2f')      # '      3.14'
format(y, '10.2e')      # '  3.14e+00'
format(y, '+10.2f')     # '     +3.14'
format(y, '+010.2f')    # '+000003.14'
format(y, '+10.2%')     # '  +314.16%'
```

### f-strings
```python
# Basic f-strings
x = 123.456
f'Value is {x:0.2f}'        # 'Value is 123.46'
f'Value is {x:10.4f}'       # 'Value is   123.4560'
f'Value is {2*x:*<10.2f}'   # 'Value is 246.91****'

# Format conversions
f'{x!r:spec}'      # Calls repr(x).__format__('spec')
f'{x!s:spec}'      # Calls str(x).__format__('spec')
```

### Format Method
```python
# Basic format
'Value is {:0.2f}'.format(x)            # 'Value is 123.46'
'Value is {0:10.2f}'.format(x)          # 'Value is   123.4560'
'Value is {val:<*10.2f}'.format(val=x)  # 'Value is 123.46****'

# Advanced formatting
y = 3.1415926
width = 8
precision = 3
'Value is {0:{1}.{2}f}'.format(y, width, precision)

# Dictionary formatting
d = {
    'name': 'IBM',
    'shares': 50,
    'price': 490.1
}
'{0[shares]:d} shares of {0[name]} at {0[price]:0.2f}'.format(d)
# '50 shares of IBM at 490.10'
```

## Command Line Arguments

### Basic Argument Handling
```python
def main(argv):
    if len(argv) != 3:
        raise SystemExit(
            f'Usage: python {argv[0]} inputfile outputfile\n')
    inputfile = argv[1]
    outputfile = argv[2]
    # ...

if __name__ == '__main__':
    import sys
    main(sys.argv)
```

### Using argparse
```python
import argparse

def main(argv):
    p = argparse.ArgumentParser(description='This is some program')

    # Positional argument
    p.add_argument('infile')

    # Option with argument
    p.add_argument('-o', '--output', action='store')

    # Boolean flag
    p.add_argument('-d', '--debug', action='store_true', default=False)

    # Parse arguments
    args = p.parse_args(args=argv)

    # Access arguments
    infile = args.infile
    output = args.output
    debugmode = args.debug

if __name__ == '__main__':
    import sys
    main(sys.argv)
```

## Best Practices

1. Use context managers for file operations
2. Handle encoding explicitly
3. Use appropriate format specifiers
4. Validate command-line arguments
5. Document I/O requirements
6. Handle errors gracefully
7. Consider performance implications
8. Follow platform conventions