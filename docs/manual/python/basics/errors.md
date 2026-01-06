# Error Handling in Python

Practical patterns for exceptions, control flow, and diagnostics.

## try/except/else/finally

```python
def load(path: str) -> str:
    try:
        with open(path, encoding="utf-8") as f:
            return f.read()
    except FileNotFoundError:
        return ""
    else:
        ...  # runs only if no exception
    finally:
        ...  # always runs
```

---

## Raise and Chain

```python
try:
    parse(x)
except ValueError as e:
    raise RuntimeError("invalid input") from e   # preserve cause

try:
    risky()
except SomeNoiseError:
    raise KeyError("clean message") from None    # hide internal chain
```

---

## Custom Exceptions

```python
class AppError(Exception):
    pass

class ConfigError(AppError):
    pass

def get_port(cfg):
    if "port" not in cfg:
        raise ConfigError("missing 'port'")
```

---

## Suppress and Context Managers

```python
from contextlib import suppress

with suppress(FileNotFoundError):
    os.remove("tmp.txt")
```

---

## EAFP vs LBYL

- EAFP (Easier to Ask Forgiveness than Permission): try the operation and handle errors.
- LBYL (Look Before You Leap): check conditions first. Prefer EAFP for concurrent/IO scenarios.

```python
# EAFP
try:
    cache[key]
except KeyError:
    cache[key] = compute()
```

---

## Logging Exceptions

```python
import logging
log = logging.getLogger(__name__)

try:
    work()
except Exception:
    log.exception("work failed")  # includes traceback
```

---

## Sentinel Values

```python
_MISSING = object()

def get(d, k):
    v = d.get(k, _MISSING)
    if v is _MISSING:
        raise KeyError(k)
    return v
```

---

## Validation Helpers

```python
def expect(cond: bool, msg: str = ""):
    if not cond:
        raise AssertionError(msg)
```

Use `AssertionError` for internal invariants only; surface user-visible errors with explicit exception types.

Next: [Builtins](builtins.md)
