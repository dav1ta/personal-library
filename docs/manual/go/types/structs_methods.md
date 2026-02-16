# Types & Interfaces

Core guide to structs, methods, interfaces, and embedding.

## Structs and Methods
Methods attach behavior to types.

### Defining Methods
Attach methods to types to add behavior.

```go
type Counter struct{ n int }

func (c *Counter) Inc() { c.n++ }
func (c Counter) Value() int { return c.n }
```

### Pointer vs Value Receivers
Choose receivers based on mutability and size.

- Use pointer receivers to mutate or avoid copying large structs.
- Value receivers work for small, immutable types.

### Method Sets
Which methods are available on a type or pointer.

```go
type Reader interface {
    Read(p []byte) (int, error)
}

type File struct{}
func (f *File) Read(p []byte) (int, error) { return 0, nil }

var r Reader
r = &File{} // ok
// r = File{} // not ok, method set missing pointer receiver
```

### Constructor Pattern
Create constructors to enforce invariants and defaults.

```go
func NewCounter() *Counter {
    return &Counter{}
}
```

## Interfaces
Interfaces are satisfied implicitly. Keep them small and focused.

### Basic Interface
The simplest form of interface declarations and usage.

```go
type Stringer interface {
    String() string
}
```

### Implementing Implicitly
How types satisfy interfaces without explicit declarations.

```go
type User struct{ Name string }

func (u User) String() string { return u.Name }
```

### Type Assertions
Extract concrete types from interfaces safely.

```go
var v any = "hello"
s, ok := v.(string)
```

### Type Switches
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

### Nil Interface Pitfall
How nil interfaces can hide a non-nil concrete value.

```go
var r io.Reader
var f *os.File = nil
r = f

fmt.Println(r == nil) // false, type is set
```

## Embedding and Composition
Go favors composition over inheritance. Embedding promotes fields and methods.

### Struct Embedding
Compose structs by embedding fields.

```go
type Logger struct {
    *log.Logger
}

type Server struct {
    Logger
    addr string
}
```

`Server` now has access to `Logger` methods directly.

### Interface Embedding
Compose interfaces by embedding smaller ones.

```go
type ReadWriteCloser interface {
    io.Reader
    io.Writer
    io.Closer
}
```

### Guidelines
Practical do and do not guidance for structure.

- Embed for reuse, not for deep type hierarchies.
- Prefer explicit fields when it improves clarity.

Next: [Concurrency](../concurrency/overview.md)
