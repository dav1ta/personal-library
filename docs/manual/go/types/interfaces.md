# Interfaces

Interfaces are satisfied implicitly. Keep them small and focused.

## Basic Interface
The simplest form of interface declarations and usage.

```go
type Stringer interface {
    String() string
}
```

## Implementing Implicitly
How types satisfy interfaces without explicit declarations.

```go
type User struct{ Name string }

func (u User) String() string { return u.Name }
```

## Type Assertions
Extract concrete types from interfaces safely.

```go
var v any = "hello"
s, ok := v.(string)
```

## Type Switches
Branch on dynamic interface types with type switches.

```go
switch v := x.(type) {
case int:
    _ = v
case string:
    _ = v
default:
}
```

## Nil Interface Pitfall
How nil interfaces can hide a non-nil concrete value.

```go
var r io.Reader
var f *os.File = nil
r = f

fmt.Println(r == nil) // false, type is set
```

Next: [Embedding](embedding.md)
