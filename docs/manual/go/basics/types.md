# Go Types and Literals

Practical overview of Go's basic and composite types.

## Basic Types
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
```go
var i int            // 0
var s string         // ""
var b bool           // false
var p *int           // nil
var m map[string]int // nil
```

## Composite Types
- array: fixed length
- slice: dynamic view over an array
- map: hash table
- struct: named fields
- interface: method set
- function, channel

```go
type User struct {
    ID   int
    Name string
}

var ids [3]int
var names []string
var ages map[string]int
var u User
```

## Literals and Inference
```go
x := 10          // int
y := 3.14        // float64
z := "hi"        // string
nums := []int{1, 2, 3}
m := map[string]int{"a": 1}
```

## Conversions (Explicit Only)
```go
var a int = 10
var b int64 = int64(a)
var f float64 = float64(a)
```

## Defined Types vs Aliases
```go
type UserID int      // new defined type
type Bytes = []byte  // alias
```

Defined types do not implicitly convert to their underlying type.

## Constants
Untyped constants adapt to context.

```go
const Pi = 3.14159
var f64 float64 = Pi
var f32 float32 = Pi
```
