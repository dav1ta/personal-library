# Maps and Sets

Maps are hash tables. Sets are usually implemented as `map[T]struct{}`.

## Maps
Key/value associative type and common operations.

```go
m := make(map[string]int)
m["a"] = 1
m["b"] = 2

v, ok := m["a"]   // ok idiom
delete(m, "b")
```

Map literals:
```go
prices := map[string]float64{
    "AAPL": 189.2,
    "MSFT": 421.1,
}
```

Notes:
- Map keys must be comparable.
- Iteration order is not guaranteed.
- A nil map can be read from but not assigned to.

## Sets
Model sets using maps for membership checks.

```go
set := map[string]struct{}{}
set["alice"] = struct{}{}

_, exists := set["alice"]
delete(set, "alice")
```

## Concurrency
Maps are not safe for concurrent writes. Guard with a mutex or use `sync.Map`.

Next: [Structs](structs.md)
