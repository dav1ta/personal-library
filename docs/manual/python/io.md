# Python `io` Module: Practical Patterns

For I/O basics, see `basics/io.md`.

A focused guide to powerful, real-world uses of `io` — especially `io.TextIOWrapper`, `io.BytesIO`, `io.StringIO`, and the buffered/raw stream stack.

## When to Use What

- `io.StringIO`: in-memory text buffer; great for capturing `print` output or building text before writing.
- `io.BytesIO`: in-memory bytes buffer; ideal for binary data (images, zips) without touching disk.
- `io.TextIOWrapper`: decode/encode a buffered binary stream to text with a specific encoding and newline policy.
- `io.BufferedReader`/`io.BufferedWriter`/`io.BufferedRandom`: add buffering around raw streams for performance.
- `io.FileIO`: raw, unbuffered file interface (backed by an OS file descriptor).
- `io.BufferedRWPair`: join a reader and writer into one duplex interface (e.g., pipes).

---

## Wrap Binary → Text with Encoding (`TextIOWrapper`)

Convert any binary stream into text with a specific encoding and newline behavior.

```python
import io

# Start with a binary source (file opened in 'rb', socket.makefile('rb'), gzip.GzipFile, etc.)
raw_bin = open("data.txt", "rb", buffering=0)

# Add buffering for performance
buffered = io.BufferedReader(raw_bin)

# Decode bytes → str with UTF-8 and universal newlines
text = io.TextIOWrapper(buffered, encoding="utf-8", newline=None)

for line in text:  # iterates by text lines
    print(line.rstrip("\n"))

# Change encoding or errors policy on the fly (Python 3.7+)
text.reconfigure(encoding="utf-8", errors="replace")

# Access underlying buffered/binary layers if needed
buf = text.buffer     # -> BufferedReader
raw = buf.raw         # -> FileIO

# Detach to reuse the buffered/raw stream; 'text' becomes unusable after this
buf2 = text.detach()
```

Notes:
- `newline=None` enables universal newline handling on reads and translates `"\n"` to the platform default on writes.
- `newline=""` disables newline translation entirely.

---

## Read/Write Compressed Text Cleanly (gzip + `TextIOWrapper`)

```python
import io, gzip

with gzip.open("logs.gz", "rb") as gz:
    # Wrap the binary gzip stream as text with decoding
    with io.TextIOWrapper(gz, encoding="utf-8", newline="\n") as txt:
        for line in txt:
            process(line)  # str lines
```

---

## In-Memory Text: Capture or Build with `StringIO`

Capture printed output, template renders, or CSV writes without a temp file.

```python
import io, sys
from contextlib import redirect_stdout

buf = io.StringIO()
with redirect_stdout(buf):
    print("hello")
    print("world")

result = buf.getvalue()     # "hello\nworld\n"
buf.close()
```

---

## In-Memory Binary: Build a ZIP Without Disk (`BytesIO`)

```python
import io, zipfile

mem = io.BytesIO()
with zipfile.ZipFile(mem, mode="w", compression=zipfile.ZIP_DEFLATED) as zf:
    zf.writestr("readme.txt", "Hi from memory!\n")

# Send over network or write once to disk
payload = mem.getvalue()
with open("bundle.zip", "wb") as f:
    f.write(payload)
```

---

## Zero-Copy Reads with `readinto` and `memoryview`

Avoid intermediate allocations when parsing binary protocols.

```python
import io, os

# Raw → buffered for performance
raw = io.FileIO("image.bin", "rb")
buf = io.BufferedReader(raw)

header = bytearray(16)
# Fill preallocated buffer in-place (no new bytes object)
read = buf.readinto(header)
if read < 16:
    raise EOFError("truncated header")

magic = header[:4]
size = int.from_bytes(header[4:8], "big")
```

---

## Start from an OS FD: `FileIO` → Buffered → Text

Useful when you already have a file descriptor (pipes, `os.open`, `subprocess`).

```python
import io, os

fd = os.open("data.txt", os.O_RDONLY)
try:
    raw = io.FileIO(fd, closefd=True)         # takes ownership of fd
    buf = io.BufferedReader(raw, buffer_size=64 * 1024)
    text = io.TextIOWrapper(buf, encoding="utf-8")

    for line in text:
        handle(line)
finally:
    # Closing 'text' cascades down unless detached
    try:
        text.close()
    except NameError:
        pass
```

---

## Duplex Pipes: `BufferedRWPair`

Combine separate read/write binary streams (e.g., from `subprocess.Popen`) into one object.

```python
import io, subprocess

p = subprocess.Popen(
    ["python", "-u", "worker.py"],
    stdin=subprocess.PIPE,
    stdout=subprocess.PIPE,
    bufsize=0,  # unbuffered at OS level; we'll buffer via io
)

rw = io.BufferedRWPair(p.stdout, p.stdin, buffer_size=64 * 1024)
text = io.TextIOWrapper(rw, encoding="utf-8", newline="\n")

text.write("PING\n"); text.flush()
reply = text.readline()
```

---

## Peek and Partial Reads (`BufferedReader.peek` / `read1`)

When implementing parsers or non-blocking protocols, small reads give better control.

```python
import io

with open("proto.bin", "rb", buffering=0) as f:
    buf = io.BufferedReader(f)

    # Look ahead without consuming
    ahead = buf.peek(4)
    if ahead.startswith(b"\x89PNG"):
        parse_png(buf)

    # Read up to N bytes using a single underlying OS read
    chunk = buf.read1(4096)
```

---

## Robust Text Decoding with Fallbacks

Handle unknown encodings gracefully.

```python
import io

bin_stream = open("unknown.txt", "rb", buffering=0)
buf = io.BufferedReader(bin_stream)
text = io.TextIOWrapper(buf, encoding="utf-8", errors="strict")

try:
    data = text.read()
except UnicodeDecodeError:
    text.reconfigure(encoding="utf-8", errors="replace")
    text.seek(0)
    data = text.read()
```

---

## Tips

- Prefer the stack: `FileIO` → `Buffered*` → `TextIOWrapper` (text) for performance and clarity.
- Use `TextIOWrapper.reconfigure(...)` to tweak encoding/errors/newline without reopening.
- Use `detach()` when you need to keep using the underlying buffered/raw stream.
- `StringIO`/`BytesIO` are perfect for tests — fast, isolated, and no filesystem side effects.
- For line-oriented network protocols, wrap sockets (or `socket.makefile('rb')`) in `BufferedReader` + `TextIOWrapper` with `newline='\n'` and explicit `.flush()`.

---

## See Also

- `io` ABCs: `io.IOBase`, `io.RawIOBase`, `io.BufferedIOBase`, `io.TextIOBase` for shared API surface.
- Standard library friends: `gzip`, `bz2`, `lzma`, `zipfile`, `tarfile` — all play well with `io` wrappers.

Next: [Structure](basics/structure.md)
