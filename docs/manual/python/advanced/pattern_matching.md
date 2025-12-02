# Structural Pattern Matching (Advanced)

Leverage `match`/`case` for expressive, declarative dispatch with classes, sequences, and mappings.

## Class Patterns and `__match_args__`

```python
from dataclasses import dataclass

@dataclass
class Point:
    x: int
    y: int
    __match_args__ = ("x", "y")  # optional if dataclass order is fine

def quadrant(p: Point) -> str:
    match p:
        case Point(x, y) if x >= 0 and y >= 0: return "Q1"
        case Point(x, y) if x < 0 <= y:        return "Q2"
        case Point(x, y) if y < 0 and x < 0:   return "Q3"
        case Point(x, y) if y < 0 <= x:        return "Q4"
        case _:                                 return "origin?"
```

Notes:
- Class patterns deconstruct attributes by name via `__match_args__` or explicit keywords: `case Point(x=0, y=y): ...`.
- Guards (`if ...`) refine matches; they must be side-effect free.

---

## Sequence and Mapping Patterns

```python
def parse_triplet(obj):
    match obj:
        case [x, y, z]:
            return (x, y, z)
        case {"x": x, "y": y}:  # mapping keys
            return (x, y, 0)
        case _:
            raise ValueError("unsupported shape")
```

Spreading with `*rest` is allowed; nested patterns compose naturally.

---

## OR-Patterns and Literal Patterns

```python
def is_affirmative(s: str) -> bool:
    match s.lower():
        case "y" | "yes" | "true" | "1":
            return True
        case _:
            return False
```

---

## Match on Enums and Types

```python
from enum import Enum

class Op(Enum): ADD = 1; MUL = 2

def eval(ast):
    match ast:
        case (Op.ADD, a, b):
            return a + b
        case (Op.MUL, a, b):
            return a * b
```

---

## Pitfalls and Best Practices

- Do not use match for arbitrary dynamic conditions; prefer `if` when no structural deconstruction is needed.
- Avoid overlapping patterns; order matters and first match wins.
- Be explicit with mapping keys to avoid accidental matches.
- Keep guards side-effect free and fast; heavy checks belong after the match.

