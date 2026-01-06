# Test Patterns

## Interfaces for Mocks
Use interfaces to make dependencies mockable.

```go
type Clock interface {
    Now() time.Time
}
```

Provide a fake in tests and a real implementation in production.

## httptest
HTTP testing helpers for servers and clients.

```go
srv := httptest.NewServer(http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
    w.Write([]byte("ok"))
}))
defer srv.Close()
```

## Golden Files
Store expected output in `testdata/` and compare.

## Parallel Tests
Run tests in parallel safely.

```go
func TestFoo(t *testing.T) {
    t.Parallel()
    ...
}
```

Next: [Generics](../advanced/generics.md)
