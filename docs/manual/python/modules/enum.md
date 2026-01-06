# Enums in Python

Use enums for clear, type-safe symbolic constants.

## Basic `Enum`

```python
from enum import Enum, auto

class Status(Enum):
    PENDING = auto()
    RUNNING = auto()
    DONE = auto()

print(Status.PENDING.name, Status.PENDING.value)
```

---

## `IntEnum` for Interop

```python
from enum import IntEnum

class Exit(IntEnum):
    OK = 0
    ERROR = 1

def run() -> Exit:
    return Exit.OK

code: int = run()  # OK: IntEnum is a subclass of int
```

---

## Bit Flags with `Flag`

```python
from enum import Flag, auto

class Perm(Flag):
    READ = auto()
    WRITE = auto()
    EXEC = auto()

mask = Perm.READ | Perm.WRITE
if Perm.READ in mask:
    ...
```

---

## Uniqueness and Aliases

```python
from enum import Enum, unique

@unique
class Color(Enum):
    RED = 1
    CRIMSON = 1  # ValueError with @unique (alias otherwise)
```

---

## Serialize / Deserialize

```python
import json
from enum import Enum

class Role(Enum):
    ADMIN = "admin"
    USER = "user"

def to_json(obj):
    if isinstance(obj, Enum):
        return obj.value
    raise TypeError

s = json.dumps({"role": Role.ADMIN}, default=to_json)
data = json.loads(s)
role = Role(data["role"])  # value → enum
```

---

## Tips

- Prefer `Enum` with explicit values for stable serialization; use strings for readability.
- Use `IntEnum` only when APIs require integers.
- For flags, prefer `Flag` over manual bit masks and test with membership (`in`).

Next: [Dataclasses](dataclasses.md)
