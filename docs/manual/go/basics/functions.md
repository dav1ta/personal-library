# Functions

Functions are first-class values and support multiple return values.

## Basic Declaration
```go
func Add(x, y int) int {
    return x + y
}
```

## Multiple Returns
```go
func Split(s string) (string, string) {
    parts := strings.SplitN(s, ":", 2)
    if len(parts) == 1 {
        return parts[0], ""
    }
    return parts[0], parts[1]
}
```

## Named Returns
```go
func Div(a, b float64) (q, r float64) {
    q = a / b
    r = math.Mod(a, b)
    return
}
```

## Variadic Functions
```go
func Sum(nums ...int) int {
    total := 0
    for _, n := range nums {
        total += n
    }
    return total
}
```

## Closures
```go
func Counter() func() int {
    n := 0
    return func() int {
        n++
        return n
    }
}
```

## Defer
```go
func ReadAll(path string) ([]byte, error) {
    f, err := os.Open(path)
    if err != nil {
        return nil, err
    }
    defer f.Close()
    return io.ReadAll(f)
}
```
