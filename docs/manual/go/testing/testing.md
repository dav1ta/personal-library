# Testing

Go uses the `testing` package with `go test`.

## Basic Test
A minimal unit test structure using the testing package.

```go
func TestAdd(t *testing.T) {
    if Add(1, 2) != 3 {
        t.Fatalf("expected 3")
    }
}
```

## Table-Driven Tests
Use tables to cover many test cases.

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
Helper functions to reduce test boilerplate.

```go
func must(t *testing.T, err error) {
    t.Helper()
    if err != nil {
        t.Fatal(err)
    }
}
```

## Benchmarks
Measure performance with testing.B benchmarks.

```go
func BenchmarkEncode(b *testing.B) {
    for i := 0; i < b.N; i++ {
        _ = encode(data)
    }
}
```

Useful helpers:

```go
func BenchmarkParse(b *testing.B) {
    b.ReportAllocs()
    b.ResetTimer()
    for i := 0; i < b.N; i++ {
        _ = parse(input)
    }
}
```

Run:

```bash
go test -bench . -benchmem
```

## Fuzzing
Coverage-guided tests to find edge cases.

```go
func FuzzParse(f *testing.F) {
    f.Add("1")
    f.Add("2")

    f.Fuzz(func(t *testing.T, in string) {
        _, _ = parse(in)
    })
}
```

Run:

```bash
go test -fuzz=FuzzParse
```

## Test Patterns

### Interfaces for Mocks
Use interfaces to make dependencies mockable.

```go
type Clock interface {
    Now() time.Time
}
```

Provide a fake in tests and a real implementation in production.

### httptest
HTTP testing helpers for servers and clients.

```go
srv := httptest.NewServer(http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
    w.Write([]byte("ok"))
}))
defer srv.Close()
```

### Golden Files
Store expected output in `testdata/` and compare.

### Parallel Tests
Run tests in parallel safely.

```go
func TestFoo(t *testing.T) {
    t.Parallel()
    ...
}
```

Next: [Advanced Topics](../advanced/generics.md)
