# Structs and Methods

Methods attach behavior to types.

## Defining Methods
Attach methods to types to add behavior.

```go
type Counter struct{ n int }

func (c *Counter) Inc() { c.n++ }
func (c Counter) Value() int { return c.n }
```

## Pointer vs Value Receivers
Choose receivers based on mutability and size.

- Use pointer receivers to mutate or avoid copying large structs.
- Value receivers work for small, immutable types.

## Method Sets
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

## Constructor Pattern
Create constructors to enforce invariants and defaults.

```go
func NewCounter() *Counter {
    return &Counter{}
}
```

Next: [Interfaces](interfaces.md)
