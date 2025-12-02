# Filesystem with `pathlib`

`pathlib.Path` provides readable, cross-platform filesystem paths and operations.

## Basics

```python
from pathlib import Path

home = Path.home()
project = home / "projects" / "demo"

# Creation
project.mkdir(parents=True, exist_ok=True)

# Read/Write text and bytes
p = project / "hello.txt"
p.write_text("hi\n", encoding="utf-8")
print(p.read_text(encoding="utf-8"))

# Iterate
for entry in project.iterdir():
    print(entry.name, entry.is_dir(), entry.stat().st_size)
```

---

## Globbing

```python
from pathlib import Path

root = Path(".")
for py in root.rglob("*.py"):
    print(py)
```

---

## Rename, Move, Delete

```python
p = Path("data.txt")
q = Path("archive") / "data-1.txt"
q.parent.mkdir(exist_ok=True)

p.replace(q)     # move/rename (atomic if same filesystem)
q.unlink(missing_ok=True)
```

---

## Paths and OS Interop

```python
from pathlib import Path
import os

p = Path("/tmp/demo.txt")
print(str(p))         # for APIs needing string paths
fd = os.open(p, os.O_RDONLY)  # interop with low-level functions

print(p.resolve())    # absolute, resolved path
print(p.exists())
print(p.is_file())
```

---

## Permissions and Stats

```python
from pathlib import Path
import stat

p = Path("script.sh")
p.write_text("#!/bin/sh\necho hi\n")
p.chmod(p.stat().st_mode | stat.S_IXUSR)  # add +x for user

s = p.stat()
print(s.st_size, s.st_mtime)
```

---

## Tips

- Prefer `Path` over `os.path`; only dip to `os` for specific features.
- Use `write_text`/`read_text` with explicit `encoding`.
- Use `replace()` for atomic move when staying on the same filesystem.

