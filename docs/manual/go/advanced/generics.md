# Generics

Go supports type parameters for functions and types.

## Generic Function
A function that works over multiple types using type parameters.

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
Restrict type parameters to a set of allowed operations.

```go
type Set[T comparable] map[T]struct{}

type Number interface {
    ~int | ~int64 | ~float64
}
```

## When to Use
Guidance on when this approach is appropriate.

- Use generics for reusable algorithms and data structures.
- Avoid generics when a simple interface or concrete type is clearer.

Next: [Reflection](reflection.md)
