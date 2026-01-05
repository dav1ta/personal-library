# Structs and Methods

Methods attach behavior to types.

## Defining Methods
```go
type Counter struct{ n int }

func (c *Counter) Inc() { c.n++ }
func (c Counter) Value() int { return c.n }
```

## Pointer vs Value Receivers
- Use pointer receivers to mutate or avoid copying large structs.
- Value receivers work for small, immutable types.

## Method Sets
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
```go
func NewCounter() *Counter {
    return &Counter{}
}
```
