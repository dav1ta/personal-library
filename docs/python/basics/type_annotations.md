# Type Annotations in Python

Type annotations allow you to indicate the expected data types of variables, function arguments, and return values. They help with code readability and enable better tooling and static analysis.

## Basic Usage

```python
def add(x: int, y: int) -> int:
    return x + y

name: str = "Alice"
age: int = 30
```

## Common Types
- `int`, `float`, `str`, `bool`
- `List`, `Dict`, `Tuple`, `Set` (from `typing` module)

## Example with Collections
```python
from typing import List, Dict

def process_items(items: List[str]) -> Dict[str, int]:
    return {item: len(item) for item in items}
```

## Optional and Union
```python
from typing import Optional, Union

def get_name(user: dict) -> Optional[str]:
    return user.get("name")

def parse_value(val: Union[int, str]) -> str:
    return str(val)
```

## Benefits
- Better editor support
- Early error detection
- Improved documentation

## Resources
- [PEP 484](https://peps.python.org/pep-0484/)
- [Python typing docs](https://docs.python.org/3/library/typing.html) 