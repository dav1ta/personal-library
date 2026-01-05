# Arrays and Slices

Arrays are fixed-size value types. Slices are dynamic views over arrays.

## Arrays
```go
var a [3]int
b := [3]int{1, 2, 3}
c := [...]int{10, 20, 30} // length inferred

a[0] = 5
fmt.Println(a == b) // comparable by element
```

Passing an array copies it. Use a pointer or slice when you want sharing.

## Slices
```go
s := []int{1, 2, 3}
t := make([]int, 0, 10) // len 0, cap 10

t = append(t, 1, 2, 3)
u := make([]int, len(t))
copy(u, t)
```

Nil vs empty:
```go
var n []int   // nil
e := []int{}  // empty but non-nil
```

## Slicing and Capacity
```go
a := [5]int{0, 1, 2, 3, 4}
s := a[1:4]      // [1 2 3]
cap(s)           // 4 (from index 1 to end)
t := a[1:4:4]    // full slice expression, cap limited to 3
```

## Pitfalls
- Appending may reallocate; do not keep pointers to elements across append.
- Slicing a huge backing array keeps it alive. Copy into a new slice if needed.

```go
big := make([]byte, 1<<20)
small := append([]byte(nil), big[:10]...) // copy to release big
```
