# Core Data Types

A focused guide to Go’s core data types: basic types, arrays, slices, maps, sets, and structs.

## Basic Types
Built-in numeric, boolean, and string types in Go.

- bool
- int, int8, int16, int32, int64
- uint, uint8 (byte), uint16, uint32, uint64, uintptr
- float32, float64
- complex64, complex128
- string
- rune (alias for int32)

```go
var ok bool
var i int = 42
var u uint = 1
var f float64 = 3.14
var c complex128 = 2 + 3i
var s string = "go"
var b byte = 'A'
var r rune = 'a'
```

## Zero Values
Default values for types when not initialized.

```go
var i int
var s string
var b bool
var p *int
var m map[string]int
```

Zero values are 0, "", false, and nil for pointers and maps.

## Literals and Inference
Use literals and type inference to keep code concise.

```go
x := 10
y := 3.14
z := "hi"
nums := []int{1, 2, 3}
m := map[string]int{"a": 1}
```

## Conversions (Explicit Only)
Go requires explicit conversions between different types.

```go
var a int = 10
var b int64 = int64(a)
var f float64 = float64(a)
```

## Defined Types vs Aliases
How defined types differ from aliases and why it matters.

```go
type UserID int
type Bytes = []byte
```

Defined types do not implicitly convert to their underlying type.

## Constants
Untyped constants adapt to context.

```go
const Pi = 3.14159
var f64 float64 = Pi
var f32 float32 = Pi
```

## Composite Types Overview
Arrays, slices, maps, and structs and how they compose.

- array: fixed length
- slice: dynamic view over an array
- map: hash table
- struct: named fields
- interface: method set
- function, channel

## Arrays
Fixed-length sequences; use when size is known at compile time.

```go
var a [3]int
b := [3]int{1, 2, 3}
c := [...]int{10, 20, 30}

a[0] = 5
same := a == b
_ = same
```

Passing an array copies it. Use a pointer or slice when you want sharing.

## Slices
Dynamic views over arrays with length and capacity.

```go
s := []int{1, 2, 3}
t := make([]int, 0, 10)

t = append(t, 1, 2, 3)
u := make([]int, len(t))
copy(u, t)
```

Nil vs empty:

```go
var n []int
u := []int{}
```

## Slicing and Capacity
How slicing affects length and capacity.

```go
a := [5]int{0, 1, 2, 3, 4}
s := a[1:4]
capS := cap(s)
t := a[1:4:4]
_ = capS
_ = t
```

## Slice Pitfalls
Common mistakes and how to avoid them.

- Appending may reallocate; do not keep pointers to elements across append.
- Slicing a huge backing array keeps it alive. Copy into a new slice if needed.

```go
big := make([]byte, 1<<20)
small := append([]byte(nil), big[:10]...)
```

## Maps
Key/value associative type and common operations.

```go
m := make(map[string]int)
m["a"] = 1
m["b"] = 2

v, ok := m["a"]
_ = v
_ = ok
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
_ = exists
delete(set, "alice")
```

## Structs
Structs group related fields into a named type.

```go
type User struct {
    ID   int
    Name string
}

u := User{ID: 1, Name: "Ada"}
v := User{1, "Linus"}
```

## Struct Zero Values
Default values for types when not initialized.

```go
var u User
_ = u.ID
_ = u.Name
```

## Pointers to Structs
Use struct pointers for mutation and efficiency.

```go
u := &User{ID: 1}
u.Name = "Ada"
```

## Struct Tags
Annotate struct fields for encoding and validation.

```go
type Person struct {
    Name string `json:"name"`
    Age  int    `json:"age,omitempty"`
}
```

## Comparability
Structs are comparable if all fields are comparable.

```go
type Point struct{ X, Y int }
same := Point{1, 2} == Point{1, 2}
_ = same
```

For non-comparable fields (slices, maps), use a custom equality function.

Next: [Functions](functions.md)
