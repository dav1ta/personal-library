# Functions

Functions are first-class values and support multiple return values.

## Basic Declaration
How to declare a function with parameters and a return type.

```go
func Add(x, y int) int {
    return x + y
}
```

## Multiple Returns
Return multiple values and handle errors idiomatically.

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
Named return values act like predeclared variables; a bare `return` uses their current values.
```go
func Div(a, b float64) (q, r float64) {
    q = a / b
    r = math.Mod(a, b)
    return
}
```

## Variadic Functions
Functions that accept a variable number of arguments.

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
Functions that capture variables from their surrounding scope.

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
`defer` schedules a function call to run when the surrounding function returns; useful for cleanup.
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

Next: [Packages & Modules](packages.md)
