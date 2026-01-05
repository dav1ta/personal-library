# Testing

Go uses the `testing` package with `go test`.

## Basic Test
```go
func TestAdd(t *testing.T) {
    if Add(1, 2) != 3 {
        t.Fatalf("expected 3")
    }
}
```

## Table-Driven Tests
```go
func TestParse(t *testing.T) {
    cases := []struct {
        in   string
        want int
    }{
        {"1", 1},
        {"2", 2},
    }

    for _, tc := range cases {
        t.Run(tc.in, func(t *testing.T) {
            got, _ := strconv.Atoi(tc.in)
            if got != tc.want {
                t.Fatalf("got %d want %d", got, tc.want)
            }
        })
    }
}
```

## Helpers
```go
func must(t *testing.T, err error) {
    t.Helper()
    if err != nil {
        t.Fatal(err)
    }
}
```
