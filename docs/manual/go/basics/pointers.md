# Pointers

Pointers let you share and mutate values.

## Basics
```go
x := 10
px := &x   // *int
*px = 20   // x is now 20
```

## Pointer Receivers
Use a pointer receiver when you need to mutate the receiver or avoid copying.

```go
type Counter struct{ n int }

func (c *Counter) Inc() { c.n++ }
func (c Counter) Value() int { return c.n }
```

## new vs &
```go
p1 := new(int) // zeroed int
p2 := &User{}  // literal
```

## Common Pitfall: Loop Variable Address
```go
nums := []int{1, 2, 3}
ptrs := []*int{}
for _, v := range nums {
    v := v // shadow copy
    ptrs = append(ptrs, &v)
}
```
