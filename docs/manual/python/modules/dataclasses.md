# Dataclasses: Practical Patterns

`dataclasses` reduces boilerplate for simple data containers while integrating cleanly with typing.

## Quick Start

```python
from dataclasses import dataclass

@dataclass
class User:
    id: int
    name: str
    active: bool = True

u = User(1, "alice")
print(u)               # User(id=1, name='alice', active=True)
```

---

## Defaults and Factories

```python
from dataclasses import dataclass, field
from typing import List

@dataclass
class Basket:
    owner: str
    items: List[str] = field(default_factory=list)  # avoid mutable default

b = Basket("alice")
b.items.append("apple")
```

---

## Frozen, Slots, KW-only

```python
from dataclasses import dataclass

@dataclass(frozen=True, slots=True, kw_only=True)
class Point:
    x: float
    y: float

p = Point(x=1.0, y=2.0)
# p.x = 2.0  # FrozenInstanceError
```

Notes:
- `frozen=True` makes instances immutable (hashable if fields are hashable).
- `slots=True` reduces memory and speeds attribute access.
- `kw_only=True` forces keyword-only init parameters (3.10+).

---

## Post-init Validation

```python
from dataclasses import dataclass

@dataclass
class Fraction:
    num: int
    den: int

    def __post_init__(self):
        if self.den == 0:
            raise ValueError("denominator cannot be 0")
```

---

## Excluding/Controlling Fields

```python
from dataclasses import dataclass, field

@dataclass(order=True)
class Job:
    priority: int
    task: str = field(compare=False)
    secret: str = field(repr=False, default="")

jobs = sorted([Job(2, "b"), Job(1, "a")])  # compare by priority only
```

---

## Converters and Metadata

```python
from dataclasses import dataclass, field

def to_int(x):
    return int(x)

@dataclass
class Config:
    workers: int = field(default=4, metadata={"env": "APP_WORKERS"})
    port: int = field(default=8080, metadata={"env": "APP_PORT"})

    def __post_init__(self):
        import os
        for f in self.__dataclass_fields__.values():
            env = f.metadata.get("env")
            if env and env in os.environ:
                setattr(self, f.name, to_int(os.environ[env]))
```

---

## Utility APIs: `asdict`, `astuple`, `replace`

```python
from dataclasses import dataclass, asdict, astuple, replace

@dataclass
class User:
    id: int
    name: str
    roles: list[str]

u = User(1, "alice", ["admin"])    
d = asdict(u)                         # deep-copy of fields
t = astuple(u)
u2 = replace(u, name="bob")
```

---

## Inheritance

```python
from dataclasses import dataclass

@dataclass
class Animal:
    name: str

@dataclass
class Dog(Animal):
    breed: str
```

If mixing with non-dataclass bases, use `init=False` and define custom `__init__` as needed.

---

## Tips

- Prefer `slots=True` for long-lived, many-instance classes.
- Use `field(default_factory=...)` for mutable defaults.
- Keep dataclasses simple; move logic to methods or helpers to avoid turning them into heavy models.
- For JSON, pair with a custom `default` function (see `modules/serialization.md`).

