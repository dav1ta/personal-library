# Memory-Mapped Files (`mmap`)

Map files directly into memory for zero-copy I/O and random access.

## Basic Read/Write Mapping

```python
import mmap, os

with open("data.bin", "r+b") as f:
    mm = mmap.mmap(f.fileno(), 0)  # map entire file
    print(mm[:10])                 # bytes-like view
    mm[0:4] = b"ABCD"             # in-place write
    mm.flush()                     # ensure changes reach disk
    mm.close()
```

`mmap` exposes the buffer protocol; wrap with `memoryview` for slicing and zero-copy parsing.

---

## Anonymous Mapping (Shared Memory Segment)

```python
mm = mmap.mmap(-1, 4096)  # not file-backed
mv = memoryview(mm)
mv[:4] = b"TEST"
```

Use for IPC with forked processes or as a large scratch buffer.

---

## Parse Structs In-Place

```python
import struct

with open("header.bin", "rb") as f:
    mm = mmap.mmap(f.fileno(), 0, access=mmap.ACCESS_READ)
    magic, size = struct.unpack_from(
        ">4sI", mm, offset=0
    )
```

`struct.unpack_from` reads from a buffer without creating a temporary slice.

---

## Tips

- Open files in binary mode; pass size 0 to map entire file.
- Use `mmap.ACCESS_COPY` for copy-on-write mappings.
- On Windows, close mappings before deleting/renaming files.
- Consider `pathlib.Path.open` and sizes from `Path.stat().st_size` for robustness.

