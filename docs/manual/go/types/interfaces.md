# Interfaces

Interfaces are satisfied implicitly. Keep them small and focused.

## Basic Interface
```go
type Stringer interface {
    String() string
}
```

## Implementing Implicitly
```go
type User struct{ Name string }

func (u User) String() string { return u.Name }
```

## Type Assertions
```go
var v any = "hello"
s, ok := v.(string)
```

## Type Switches
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
```go
var r io.Reader
var f *os.File = nil
r = f

fmt.Println(r == nil) // false, type is set
```
