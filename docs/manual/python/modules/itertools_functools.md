# Itertools + Functools Recipes

Handy combinations for efficient iteration and functional utilities.

## Itertools Essentials

```python
from itertools import islice, chain, pairwise, batched, groupby, accumulate, product, permutations, combinations

# Take first N
first10 = list(islice(range(1000), 10))

# Flatten
flat = list(chain.from_iterable([[1,2],[3,4]]))

# Adjacent pairs (3.10+)
pairs = list(pairwise([1,2,4,7]))  # [(1,2),(2,4),(4,7)]

# Batching (3.12+)
for chunk in batched(range(10), 3):
    print(chunk)  # (0,1,2), (3,4,5), (6,7,8), (9,)

# Group consecutive items by key
data = ["a1","a2","b1","b2","b3","a3"]
for k, grp in groupby(sorted(data, key=lambda s: s[0]), key=lambda s: s[0]):
    print(k, list(grp))

# Running totals
list(accumulate([1,2,3,4]))  # [1,3,6,10]

# Cartesian and combinatorics
list(product("ab", [1,2]))
list(permutations([1,2,3], 2))
list(combinations([1,2,3], 2))
```

---

## Functools Essentials

```python
from functools import lru_cache, cache, partial, reduce, singledispatch, total_ordering, wraps, cached_property

# Caching
@lru_cache(maxsize=1024)
def fib(n: int) -> int:
    return n if n < 2 else fib(n-1) + fib(n-2)

@cache  # unlimited (Python 3.9+)
def parse_token(s: str):
    return s.split(".")

# Partial application
from operator import mul
times10 = partial(mul, 10)
times10(7)  # 70

# Reduce
reduce(mul, [1,2,3,4], 1)  # 24

# Single-dispatch generic function
@singledispatch
def dump(x):
    return repr(x)

@dump.register
def _(x: list):
    return f"list(len={len(x)})"

# Rich comparisons from one method
@total_ordering
class Ver:
    def __init__(self, major: int, minor: int):
        self.major, self.minor = major, minor
    def __eq__(self, other):
        return (self.major, self.minor) == (other.major, other.minor)
    def __lt__(self, other):
        return (self.major, self.minor) < (other.major, other.minor)

class Service:
    def __init__(self):
        self._cfg = {"url": "https://example"}
    @cached_property
    def url(self):
        # compute once, cache on instance
        return self._cfg["url"].rstrip("/")
```

---

## Patterns and Tips

- Combine `itertools` lazily to avoid materializing intermediate lists.
- `groupby` groups consecutive items; sort or pre-group data as needed.
- Use `lru_cache` for expensive pure functions; mind memory usage and invalidation.
- Prefer `singledispatch` for open-closed polymorphism without class hierarchies.
- `cached_property` is ideal for expensive, read-mostly attributes.

Next: [Regex](regex.md)
