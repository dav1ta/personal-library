Note: See `io.md` for advanced I/O wrappers and patterns (TextIOWrapper, BytesIO, StringIO, buffering).

data representation

# Specify a bytes literal (note: b' prefix)
a = b'hello'

# Specify bytes from a list of integers
b = bytes([0x68, 0x65, 0x6c, 0x6c, 0x6f])

# Create and populate a bytearray from parts
c = bytearray()
c.extend(b'world')   # d = 'world'
c.append(0x21)       # d = 'world!'

# Access byte values
print(a[0])     # --> prints 104

for x in b:     # Outputs 104 101 108 108 111
   print(x)

a = b'hello'     # bytes
b = 'hello'      # text
c = 'world'      # text

print(a == b)    # -> False
d = a + c        # TypeError: can't concat str to bytes
e = b + c        # -> 'helloworld' (both are strings)

text encoding and decoding

a = 'hello'             # Text
b = a.encode('utf-8')   # Encode to bytes

c = b'world'            # Bytes
d = c.decode('utf-8')   # Decode to text

'ascii'

Character values in the range [0x00, 0x7f].

'latin1'

Character values in the range [0x00, 0xff]. Also known as 'iso-8859-1'.

'utf-8'

Variable-length encoding that allows all Unicode characters to be represented.

'cp1252'

A common text encoding on Windows.

'macroman'

A common text encoding on Macintosh.

text and byte formatting

x = 123.456
format(x, '0.2f')       # '123.46'
format(x, '10.4f')      # '  123.4560'
format(x, '<*10.2f')    # '123.46****'

name = 'Elwood'
r = format(name, '<10')     # r = 'Elwood    '
r = format(name, '>10')     # r = '    Elwood'
r = format(name, '^10')     # r = '  Elwood  '
r = format(name, '*^10')    # r = '**Elwood**'

d

Decimal integer or long integer.

b

Binary integer or long integer.

o

Octal integer or long integer.

x

Hexadecimal integer or long integer.

X

Hexadecimal integer (uppercase letters).

f, F

Floating point as [-]m.dddddd.

e

Floating point as [-]m.dddddde±xx.

E

Floating point as [-]m.ddddddE±xx.

g, G

Use e or E for exponents less than [nd]4 or greater than the precision; otherwise use f.

n

Same as g except that the current locale setting determines the decimal point character.

%

Multiplies a number by 100 and displays it using f format followed by a % sign.

s

String or any object. The formatting code uses str() to generate strings.

c

Single character.

x = 42
r = format(x, '10d')        # r = '        42'
r = format(x, '10x')        # r = '        2a'
r = format(x, '10b')        # r = '    101010'
r = format(x, '010b')       # r = '0000101010'

y = 3.1415926
r = format(y, '10.2f')      # r = '      3.14'
r = format(y, '10.2e')      # r = '  3.14e+00'
r = format(y, '+10.2f')     # r = '     +3.14'
r = format(y, '+010.2f')    # r = '+000003.14'
r = format(y, '+10.2%')     # r = '  +314.16%'


f'Value is {x:0.2f}'        # 'Value is 123.46'
f'Value is {x:10.4f}'       # 'Value is   123.4560'
f'Value is {2*x:*<10.2f}'   # 'Value is 246.91****'

f'{x!r:spec}'      # Calls (repr(x).__format__('spec'))
f'{x!s:spec}'      # Calls (str(x).__format__('spec'))

'Value is {:0.2f}' .format(x)            # 'Value is 123.46'
'Value is {0:10.2f}' .format(x)          # 'Value is   123.4560'
'Value is {val:<*10.2f}' .format(val=x)  # 'Value is 123.46****'

Unlike f-strings, the arg value of a specifier cannot be an arbitrary expression, so it’s not quite as expressive. However, the format() method can perform limited attribute lookup, indexing, and nested substitutions. For example:

y = 3.1415926
width = 8
precision=3

r = 'Value is {0:{1}.{2}f}'.format(y, width, precision)

d = {
   'name': 'IBM',
   'shares': 50,
   'price': 490.1
}
r = '{0[shares]:d} shares of {0[name]} at {0[price]:0.2f}'.format(d)
# r = '50 shares of IBM at 490.10'

command line arguments

def main(argv):
    if len(argv) != 3:
        raise SystemExit(
              f'Usage : python {argv[0]} inputfile outputfile\n')
    inputfile  = argv[1]
    outputfile = argv[2]
    ...

if __name__ == '__main__':
    import sys
    main(sys.argv)

import argparse

def main(argv):
    p = argparse.ArgumentParser(description='This is some program')

    # A positional argument
    p.add_argument('infile')

    # An option taking an argument
    p.add_argument('-o','--output', action='store')

    # An option that sets a boolean flag
    p.add_argument('-d','--debug', action='store_true', default=False)

    # Parse the command line
    args = p.parse_args(args=argv)

    # Retrieve the option settings
    infile    = args.infile
    output    = args.output
    debugmode = args.debug

    print(infile, output, debugmode)

if __name__ == '__main__':
    import sys
    main(sys.argv[1:])

env variables

import os
path = os.environ['PATH']
user = os.environ['USER']
editor = os.environ['EDITOR']
val = os.environ['SOMEVAR']

buffering

By default, files are opened with I/O buffering enabled. With I/O buffering, I/O operations are performed in larger chunks to avoid excessive system calls. For example, write operations would start filling an internal memory buffer and output would only actually occur when the buffer is filled up. This behavior can be changed by giving a buffering argument to open(). For example

with open('data.bin', 'wb', buffering=0) as file:
    file.write(data)
    file.write(data)
    file.write(data)
    file.flush()       # Make sure all data is written from buffers

text mode enxoding

with open('file.txt', 'rt',
          encoding='utf-8', errors='replace') as file:
    data = file.read()

newline

With text files, one complication is the encoding of newline characters. Newlines are encoded as '\n', '\r\n', or '\r' depending on the host operating system—for example, '\n' on UNIX and '\r\n' on Windows. By default, Python translates all of these line endings to a standard '\n' character when reading. On writing, newline characters are translated back to the default line ending used on the system. The behavior is sometimes referred to as “universal newline mode” in Python documentation.

file = open('somefile.txt', 'rt', newline='\r\n')

behind in scenes

The open() function serves as a kind of high-level factory function for creating instances of different I/O classes. These classes embody the different file modes, encodings, and buffering behaviors. They are also composed together in layers. The following classes are defined in the io module:

Click here to view code image

FileIO(filename, mode='r', closefd=True, opener=None) Opens a file for raw unbuffered binary I/O. filename is any valid filename accepted by the open() function. Other arguments have the same meaning as for open().

Click here to view code image

BufferedReader(file [, buffer_size]) BufferedWriter(file [, buffer_size]) BufferedRandom(file,[, buffer_size]) Implements a buffered binary I/O layer for a file. file is an instance of FileIO. buffer_size specifies the internal buffer size to use. The choice of class depends on whether or not the file is reading, writing, or updating data. The optional buffer_size argument specifies the internal buffer size used.

Click here to view code image

TextIOWrapper(buffered, [encoding, [errors [, newline [, line_buffering [, write_through]]]]]) Implements text mode I/O. buffered is a buffered binary mode file, such as BufferedReader or BufferedWriter. The encoding, errors, and newline arguments have the same meaning as for open(). line_buffering is a Boolean flag that forces I/O to be flushed on newline characters (False by default). write_through is a Boolean flag that forces all writes to be flushed (False by default).

Here is an example that shows how a text-mode file is constructed, layer-by-layer:

Click here to view code image

            raw = io.FileIO('filename.txt', 'r') # Raw-binary mode buffer = io.BufferedReader(raw) # Binary buffered reader file = io.TextIOWrapper(buffer, encoding='utf-8') # Text mode

file methods

f.readable()

Returns True if file can be read.

f.read([n])

Reads at most n bytes.

f.readline([n])

Reads a single line of input up to n characters. If n is omitted, this method reads the entire line.

f.readlines([size])

Reads all the lines and returns a list. size optionally specifies the approximate number of characters to read on the file before stopping.

f.readinto(buffer)

Reads data into a memory buffer.

f.writable()

Returns True if file can be written.

f.write(s)

Writes string s.

f.writelines(lines)

Writes all strings in iterable lines.

f.close()

Closes the file.

f.seekable()

Returns True if file supports random-access seeking.

f.tell()

Returns the current file pointer.

f.seek(offset [, where])

Seeks to a new file position.

f.isatty()

Returns True if f is an interactive terminal.

f.flush()

Flushes the output buffers.

f.truncate([size])

Truncates the file to at most size bytes.

f.fileno()

Returns an integer file descriptor.
file attributes

f.closed

Boolean value indicates the file state: False if the file is open, True if closed.

f.mode

The I/O mode for the file.

f.name

Name of the file if created using open(). Otherwise, it will be a string indicating the source of the file.

f.newlines

The newline representation actually found in the file. The value is either None if no newlines have been encountered, a string containing '\n', '\r', or '\r\n', or a tuple containing all the different newline encodings seen.

f.encoding

A string that indicates file encoding, if any (for example, 'latin-1' or 'utf-8'). The value is None if no encoding is being used.

f.errors

The error handling policy.

f.write_through

Boolean value indicating if writes on a text file pass data directly to the underlying binary level file without buffering.
stdin, stdout, stderr

import sys
sys.stdout.write('Enter your name : ')
name = sys.stdin.readline()

If necessary, the values of sys.stdout, sys.stdin, and sys.stderr can be replaced with other file objects, in which case the print() and input() functions will use the new values. Should it ever be necessary to restore the original value of sys.stdout, it should be saved first. The original values of sys.stdout, sys.stdin, and sys.stderr at interpreter startup are also available in sys.stdout, sys.stdin, and sys.stderr, respectively.

directories

import os

names = os.listdir('dirname')
for name in names:
    print(name)

print

print('The values are', x, y, z)


# Suppress the newline
print('The values are', x, y, z, end='')
To redirect the output to a file, use the file keyword argument:

# Redirect to file object f
print('The values are', x, y, z, file=f)
To change the separator character between items, use the sep keyword argument:
# Put commas between the values
print('The values are', x, y, z, sep=',')

consume input

use advance generator for io

def line_receiver():
    data = bytearray()
    line = None
    linecount = 0
    while True:
        part = yield line
        linecount += part.count(b'\n')
        data.extend(part)
        if linecount > 0:
            index = data.index(b'\n')
            line = bytes(data[:index+1])
            data = data[index+1:]
            linecount -= 1
        else:
            line = None

>>> r = line_receiver()
>>> r.send(None)    # Necessary first step
>>> r.send(b'hello')
>>> r.send(b'world\nit ')
b'hello world\n'
>>> r.send(b'works!')
>>> r.send(b'\n')
b'it works!\n''
>>>

An interesting side effect of this approach is that it externalizes the actual I/O operations that must be performed to get the input data. Specifically, the implementation of line_receiver() contains no I/O operations at all! This means that it could be used in different contexts. For example, with sockets:

r = line_receiver()
data = None
while True:
    while not (line:=r.send(data)):
        data = sock.recv(8192)

    # Process the line
    ...

r = line_receiver()
data = None
while True:
    while not (line:=r.send(data)):
        data = file.read(10000)

    # Process the line
    ...

async def reader(ch):
    r = line_receiver()
    data = None
    while True:
        while not (line:=r.send(data)):
            data = await ch.receive(8192)

object serializations

Sometimes it’s necessary to serialize the representation of an object so it can be transmitted over the network, saved to a file, or stored in a database. One way to do this is to convert data into a standard encoding such as JSON or XML. There is also a common Python-specific data serialization format called Pickle.

The pickle module serializes an object into a stream of bytes that can be used to reconstruct the object at a later point in time. The interface to pickle is simple, consisting of two operations, dump() and load(). For example, the following code writes an object to a file:

import pickle
obj = SomeObject()
with open(filename, 'wb') as file:
   pickle.dump(obj, file)      # Save object on f
To restore the object, use:

Click here to view code image

with open(filename, 'rb') as file:
    obj = pickle.load(file)   # Restore the object

It is not normally necessary for user-defined objects to do anything extra to work with pickle. However, certain kinds of objects can’t be pickled. These tend to be objects that incorporate runtime state—open files, threads, closures, generators, and so on. To handle these tricky cases, a class can define the special methods getstate() and setstate().

The getstate() method, if defined, will be called to create a value representing the state of an object. The value returned by getstate() is typically a string, tuple, list, or dictionary. The setstate() method receives this value during unpickling and should restore the state of an object from it.

do not unpickle unknow data
blocking operations and concurency

A fundamental aspect of I/O is the concept of blocking. By its very nature, I/O is connected to the real world. It often involves waiting for input or devices to be ready. For example, code that reads data on the network might perform a receive operation on a socket like this:

data = sock.recv(8192)

def reader1(sock):
    while (data := sock.recv(8192)):
        print('reader1 got:', data)

def reader2(sock):
    while (data := sock.recv(8192)):
        print('reader2 got:', data)

# Problem: How to make reader1() and reader2()
# run at the same time?

The rest of this section outlines a few different approaches to solving this problem. However, it is not meant to be a full tutorial on concurrency. For that, you will need to consult other resources.

nonblocking io

def run(sock1, sock2):
    sock1.setblocking(False)
    sock2.setblocking(False)
    while True:
        reader1(sock1)
        reader2(sock2)
In practice, relying only on nonblocking I/O is clumsy and inefficient. For example, the core of this program is the run() function at the end. It will run in a inefficient busy loop as it constantly tries to read on the sockets. This works, but it is not a good design.

IO polling

Instead of relying upon exceptions and spinning, it is possible to poll I/O channels to see if data is available. The select or selectors module can be used for this purpose. For example, here’s a slightly modified version of the run() function:

from selectors import DefaultSelector, EVENT_READ, EVENT_WRITE

def run(sock1, sock2):
    selector = DefaultSelector()
    selector.register(sock1, EVENT_READ, data=reader1)
    selector.register(sock2, EVENT_READ, data=reader2)
    # Wait for something to happen
    while True:
        for key, evt in selector.select():
            func = key.data
            func(key.fileobj)

In this code, the loop dispatches either reader1() or reader2() function as a callback whenever I/O is detected on the appropriate socket. The selector.select() operation itself blocks, waiting for I/O to occur. Thus, unlike the previous example, it won’t make the CPU furiously spin.

This approach to I/O is the foundation of many so-called “async” frameworks such as asyncio, although you usually don’t see the inner workings of the event loop.
threading

import threading

def reader1(sock):
    while (data := sock.recv(8192)):
        print('reader1 got:', data)

def reader2(sock):
    while (data := sock.recv(8192)):
        print('reader2 got:', data)

t1 = threading.Thread(target=reader1, args=[sock1]).start()
t2 = threading.Thread(target=reader2, args=[sock2]).start()

# Start the threads
t1.start()
t2.start()

# Wait for the threads to finish
t1.join()
t2.join()

asyncio

import asyncio

async def reader1(sock):
    loop = asyncio.get_event_loop()
    while (data := await loop.sock_recv(sock, 8192)):
        print('reader1 got:', data)

async def reader2(sock):
    loop = asyncio.get_event_loop()
    while (data := await loop.sock_recv(sock, 8192)):
        print('reader2 got:', data)

async def main(sock1, sock2):
    loop = asyncio.get_event_loop()
    t1 = loop.create_task(reader1(sock1))
    t2 = loop.create_task(reader2(sock2))

    # Wait for the tasks to finish
    await t1
    await t2

...
# Run it
asyncio.run(main(sock1, sock2))

asyncio tcp socket

import asyncio
from socket import *

async def echo_server(address):
    loop = asyncio.get_event_loop()
    sock = socket(AF_INET, SOCK_STREAM)
    sock.setsockopt(SOL_SOCKET, SO_REUSEADDR, 1)
    sock.bind(address)
    sock.listen(5)
    sock.setblocking(False)
    print('Server listening at', address)
    with sock:
        while True:
            client, addr = await loop.sock_accept(sock)
            print('Connection from', addr)
            loop.create_task(echo_client(loop, client))

async def echo_client(loop, client):
    with client:
        while True:
            data = await loop.sock_recv(client, 10000)
            if not data:
                break
            await loop.sock_sendall(client, b'Got:' + data)
    print('Connection closed')

if __name__ == '__main__':
    loop = asyncio.get_event_loop()
    loop.create_task(echo_server(loop, ('', 25000)))
    loop.run_forever()

To test this code, use a program such as nc or telnet to connect to port 25000 on your machine. The code should echo back the text that you type. If you connect more than once using multiple terminal windows, you’ll find that the code can handle all of the connections concurrently.

Most applications using asyncio will probably operate at a higher level than sockets. However, in such applications, you will still have to make use of special async functions and interact with the underlying event loop in some manner.
binascii

converts binary data into text repr

binascii.b2a_hex(b'hello') b'68656c6c6f'

            binascii.a2b_hex() b'hello' binascii.b2a_base64(b'hello') b'aGVsbG8=\n' binascii.a2b_base64()

cgi module

<form method='POST' action='cgi-bin/register.py'>
   <p>
   To register, please provide a contact name and email address.
   </p>
   <div>
      <input name='name' type='text'>Your name:</input>
   </div>
   <div>
      <input name='email' type='email'>Your email:</input>
   </div>
   <div class='modal-footer justify-content-center'>
      <input type='submit' name='submit' value='Register'></input>
   </div>
</form>
Here’s a CGI script that receives the form data on the other end:

Click here to view code image

#!/usr/bin/env python
import cgi
try:
    form = cgi.FieldStorage()
    name = form.getvalue('name')
    email = form.getvalue('email')
    # Validate the responses and do whatever
    ...
    # Produce an HTML result (or redirect)
    print('Status: 302 Moved\r')
    print('Location: https://www.mywebsite.com/thanks.html\r')
    print('\r')
except Exception as e:
    print('Status: 501 Error\r')
    print('Content-type: text/plain\r')
    print('\r')
    print('Some kind of error occurred.\r')
Will writing such a CGI script get you a job at an Internet startup? Probably not. Will it solve your actual problem? Likely.

configparser

; A comment
[section1]
name1 = value1
name2 = value2

[section2]
; Alternative syntax
name1: value1
name2: value2

cfg = configparser.ConfigParser()
cfg.read('conig.ini')

# Extract values
a = cfg.get('section1', 'name1')
b = cfg.get('section2', 'name2')

errorno

so much error handlerrs
fcntl module

low level io tool

open file with lock to avoid concurent open

import fcntl

with open('somefile', 'r') as file:
     try:
         fcntl.flock(file.fileno(), fcntl.LOCK_EX)
         # Use the file
         ...
     finally:
         fcntl.flock(file.fileno(), fcntl.LOCK_UN)

hashlib

The hashlib module provides functions for computing cryptographic hash values such as MD5 and SHA-1. The following example illustrates how to use the module:

Click here to view code image

            h = hashlib.new('sha256') h.update(b'Hello') # Feed data h.update(b'World') h.digest() b'\xa5\x91\xa6\xd4\x0b\xf4 @J\x01\x173\xcf\xb7\xb1\x90\xd6,e\xbf\x0b\xcd\xa3+W\xb2w\xd9\xad\x9f\x14n h.hexdigest() 'a591a6d40bf420404a011733cfb7b190d62c65bf0bcda32b57b277d9ad9f146e' h.digest_size 32

https package
io

The io module primarily contains the definitions of classes used to implement the file objects as returned by the open() function. It is not so common to access those classes directly. However, the module also contains a pair of classes that are useful for “faking” a file in the form of strings and bytes. This can be useful for testing and other applications where you need to provide a “file” but have obtained data in a different way.

The StringIO() class provides a file-like interface on top of strings. For example, here is how you can write output to a string:

import io
file = io.StringIO()
greeting(file)

# Get the resulting output
output = file.getvalue()

logging

The logging module is the de facto standard module used for reporting program diagnostics and for print-style debugging. It can be used to route output to a log file and provides a large number of configuration options. A common practice is to write code that creates a Logger instance and issues messages on it like this:

Click here to view code image

import logging
log = logging.getLogger(__name__)

# Function that uses logging
def func(args):
    log.debug('A debugging message')
    log.info('An informational message')
    log.warning('A warning message')
    log.error('An error message')
    log.critical('A critical message')

# Configuration of logging (occurs one at program startup)
if __name__ == '__main__':
    logging.basicConfig(
         level=logging.WARNING,
         filename='output.log'
    )

There are five built-in levels of logging ordered by increasing severity. When configuring the logging system, you specify a level that acts as a filter. Only messages at that level or greater severity are reported. Logging provides a large number of configuration options, mostly related to the back-end handling of the log messages. Usually you don’t need to know about that when writing application code—you use debug(), info(), warning(), and similar methods on some given Logger instance. Any special configuration takes place during program startup in a special location (such as a main() function or the main code block).

pathlib

from pathlib import Path

filename = Path('/Users/beazley/old/data.csv')
Once you have an instance filename of Path, you can perform various operations on it to manipulate the filename. For example:

Click here to view code image

>>> filename.name
'data.csv'
>>> filename.parent
Path('/Users/beazley/old')
>>> filename.parent / 'newfile.csv'
Path('/Users/beazley/old/newfile.csv')
>>> filename.parts
('/', 'Users', 'beazley', 'old', 'data.csv')
>>> filename.with_suffix('.csv.clean')
Path('/Users/beazley/old/data.csv.clean')
>>>

import pathlib

def compute_usage(filename):
    pathname = pathlib.Path(filename)
    if pathname.is_file():
        return pathname.stat().st_size
    elif pathname.is_dir():
        return sum(path.stat().st_size
                   for path in pathname.rglob('*')
                   if path.is_file())
        return pathname.stat().st_size
    else:
        raise RuntimeError('Unsupported file kind')

re

regex
shutil

some shell commadns

import shutil

shutil.copy(srcfile, dstfile) To move a file:

Click here to view code image

shutil.move(srcfile, dstfile) To copy a directory tree:

Click here to view code image

shutil.copytree(srcdir, dstdir) To remove a directory tree:

shutil.rmtree(pathname) The shutil module is often used as a safer and more portable alternative to directly executing shell commands with the os.system() function.
select

The select module is used for simple polling of multiple I/O streams. That is, it can be used to watch a collection of file descriptors for incoming data or for the ability to receive outgoing data. The following example shows typical usage:

import select

# Collections of objects representing file descriptors.  Must be
# integers or objects with a fileno() method.
want_to_read = [ ... ]
want_to_write = [ ... ]
check_exceptions = [ ... ]

# Timeout (or None)
timeout = None

# Poll for I/O
can_read, can_write, have_exceptions = \
    select.select(want_to_read, want_to_write, check_exceptions, timeout)

# Perform I/O operations
for file in can_read:
    do_read(file)
for file in can_write:
    do_write(file)

# Handle exceptions
for file in have_exceptions:
    handle_exception(file)

smtlib

import smtplib

fromaddr = 'someone@some.com'
toaddrs = ['recipient@other.com' ]
amount = 123.45
msg = f'''From: {fromaddr}\r
\r
Pay {amount} bitcoin or else.  We're watching.\r
'''

server = smtplib.SMTP('localhost')
serv.sendmail(fromaddr, toaddrs, msg)
serv.quit()

socket

use telnet or nc

from socket import socket, AF_INET, SOCK_STREAM

sock = socket(AF_INET, SOCK_STREAM)
sock.connect(('python.org', 80))
sock.send(b'GET /index.html HTTP/1.0\r\n\r\n')
parts = []
while True:
    part = sock.recv(10000)
    if not part:
        break
    parts.append(part)
response = b''.join(part)
print(part)

struct

The struct module is used to convert data between Python and binary data structures, represented as Python byte strings. These data structures are often used when interacting with functions written in C, binary file formats, network protocols, or binary communication over serial ports.

As an example, suppose you need to construct a binary message with its format described by a C data structure:

Click here to view code image
Message format: All values are 'big endian'

struct Message { unsigned short msgid; // 16 bit unsigned integer unsigned int sequence; // 32 bit sequence number float x; // 32 bit float float y; // 32 bit float }
subprocess

import subprocess

# Run the 'netstat -a' command and collect its output
try:
    out = subprocess.check_output(['netstat', '-a'])
except subprocess.CalledProcessError as e:
    print('It failed:', e)
The data returned by check_output() is presented as bytes. If you want to convert it to text, make sure you apply a proper decoding:

Click here to view code image

text = out.decode('utf-8')
It is also possible to set up a pipe and to interact with a subprocess in a more detailed manner. To do that, use the Popen class like this:

Click here to view code image

import subprocess

p = subprocess.Popen(['wc'],
                     stdin=subprocess.PIPE,
                     stdout=subprocess.PIPE)

# Send data to the subprocess
p.stdin.write(b'hello world\nthis is a test\n')
p.stdin.close()

# Read data back
out = p.stdout.read()
print(out)

tmpfile

temp files
textwrap

wrapped = textwrap.wrap(text, width=81)
print('\n'.join(wrapped))

threading

threads

import threading
import time

def countdown(n):
    while n > 0:
        print('T-minus', n)
        n -= 1
        time.sleep(1)

t = threading.Thread(target=countdown, args=[10])
t.start()
t.join()      # Wait for the thread to finish
If you’re never going to wait for the thread to finish, make it daemonic by supplying an extra daemon flag like this:

Click here to view code image

t = threading.Thread(target=countdown, args=[10], daemon=True)
## to stop
import threading
import time

must_stop = False

def countdown(n):
    while n > 0 and not must_stop:
        print('T-minus', n)
        n -= 1
        time.sleep(1)

thread lock

import threading

class Counter:
    def __init__(self):
        self.value = 0
        self.lock = threading.Lock()

    def increment(self):
        with self.lock:
             self.value += 1

    def decrement(self):
        with self.lock:
             self.value -= 1

threading event

def step1(evt):
    print('Step 1')
    time.sleep(5)
    evt.set()

def step2(evt):
    evt.wait()
    print('Step 2')

evt = threading.Event()
threading.Thread(target=step1, args=[evt]).start()
threading.Thread(target=step2, args=[evt]).start()

thread queue

import threading
import queue
import time

def producer(q):
    for i in range(10):
        print('Producing:', i)
        q.put(i)
    print('Done')
    q.put(None)

def consumer(q):
    while True:
        item = q.get()
        if item is None:
            break
        print('Consuming:', item)
    print('Goodbye')

q = queue.Queue()
threading.Thread(target=producer, args=[q]).start()
threading.Thread(target=consumer, args=[q]).start()

time

The time module is used to access system time-related functions. The following selected functions are the most useful:

sleep(seconds) Make Python sleep for a given number of seconds, given as a floating point.

time() Return the current system time in UTC as a floating-point number. This is the number of seconds since the epoch (usually January 1, 1970 for UNIX systems). Use localtime() to convert it into a data structure suitable for extracting useful information.

localtime([secs]) Return a struct_time object representing the local time on the system or the time represented by the floating-point value secs passed as an argument. The resulting struct has attributes tm_year, tm_mon, tm_mday, tm_hour, tm_min, tm_sec, tm_wday, tm_yday, and tm_isdst.

gmtime([secs]) The same as localtime() except that the resulting structure represents the time in UTC (or Greenwich Mean Time).

ctime([secs]) Convert a time represented as seconds to a text string suitable for printing. Useful for debugging and logging.

asctime(tm) Convert a time structure as represented by localtime() into a text string suitable for printing.

The datetime module is more generally used for representing dates and times for the purpose of performing date-related computations and dealing with timezones.
urllib

>>> from urllib.request import urlopen
>>> u = urlopen('http://www.python.org')
>>> data = u.read()
>>>
If you want to encode form parameters, you can use urllib.parse.urlencode() as shown here:

Click here to view code image

from urllib.parse import urlencode
from urllib.request import urlopen

form = {
   'name': 'Mary A. Python',
   'email': 'mary123@python.org'
}

data = urlencode(form)
u = urlopen('http://httpbin.org/post', data.encode('utf-8'))
response = u.read()
The urlopen() function works fine for basic webpages and APIs involving HTTP or HTTPS. However, it becomes quite awkward to use if access also involves cookies, advanced authentication schemes, and other layers. Frankly, most Python programmers would use a third-party library such as requests or httpx to handle these situations. You should too.

The urllib.parse subpackage has additional functions for manipulating URLs themselves. For example, the urlparse() function can be used to pull apart a URL:

Click here to view code image

>>> url = 'http://httpbin.org/get?name=Dave&n=42'
>>> from urllib.parse import urlparse
>>> urlparse(url)
ParseResult(scheme='http', netloc='httpbin.org', path='/get', params='',
query='name=Dave&n=42', fragment='')
>>>

unicodedata

for unicode strings

unicodedata.normalize(option)
xml

from xml.etree.ElementTree import ElementTree

doc = ElementTree(file='recipe.xml')
title = doc.find('title')
print(title.text)

# Alternative (just get element text)
print(doc.findtext('description'))

# Iterate over multiple elements
for item in doc.findall('ingredients/item'):
    num = item.get('num')
    units = item.get('units', '')
    text = item.text.strip()
    print(f'{num} {units} {text}')

I/O is a fundamental part of writing any useful program. Given its popularity, Python is able to work with literally any data format, encoding, or document structure that’s in use. Although the standard library might not support it, you will almost certainly find a third-party module to solve your problem.

In the big picture, it may be more useful to think about the edges of your application. At the outer boundary between your program and reality, it’s common to encounter issues related to data encoding. This is especially true for textual data and Unicode. Much of the complexity in Python’s I/O handling—supporting different encoding, error handling policies, and so on—is aimed at this specific problem. It’s also critical to keep in mind that textual data and binary data are strictly separated. Knowing what you’re working with helps in understanding the big picture.

A secondary consideration in I/O is the overall evaluation model. Python code is currently separated into two worlds—normal synchronous code and asynchronous code usually associated with the asyncio module (characterized by the use of async functions and the async/await syntax). Asynchronous code almost always requires using dedicated libraries that are capable of operating in that environment. This, in turn, forces your hand on writing your application code in the “async” style as well. Honestly, you should probably avoid asynchronous coding unless you absolutely know that you need it—and if you’re not really sure, then you almost certainly don’t. Most of the well-adjusted Python-speaking universe codes in a normal synchronous style that is far easier to reason about, debug, and test. You should choose that.
