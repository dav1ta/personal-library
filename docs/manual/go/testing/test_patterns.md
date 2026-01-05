# Test Patterns

## Interfaces for Mocks
```go
type Clock interface {
    Now() time.Time
}
```

Provide a fake in tests and a real implementation in production.

## httptest
```go
srv := httptest.NewServer(http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
    w.Write([]byte("ok"))
}))
defer srv.Close()
```

## Golden Files
Store expected output in `testdata/` and compare.

## Parallel Tests
```go
func TestFoo(t *testing.T) {
    t.Parallel()
    ...
}
```
