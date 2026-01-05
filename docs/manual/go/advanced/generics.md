# Generics

Go supports type parameters for functions and types.

## Generic Function
```go
func Map[T any, U any](in []T, f func(T) U) []U {
    out := make([]U, len(in))
    for i, v := range in {
        out[i] = f(v)
    }
    return out
}
```

## Constraints
```go
type Set[T comparable] map[T]struct{}

type Number interface {
    ~int | ~int64 | ~float64
}
```

## When to Use
- Use generics for reusable algorithms and data structures.
- Avoid generics when a simple interface or concrete type is clearer.
