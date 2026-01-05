# Sync and Context

Use `sync` primitives for shared memory. Use `context` for cancellation and timeouts.

## Mutex and RWMutex
```go
type Cache struct {
    mu sync.RWMutex
    m  map[string]string
}

func (c *Cache) Get(k string) (string, bool) {
    c.mu.RLock()
    defer c.mu.RUnlock()
    v, ok := c.m[k]
    return v, ok
}
```

## Once and Pool
```go
var once sync.Once
once.Do(initConfig)

var pool = sync.Pool{New: func() any { return make([]byte, 0, 4096) }}
```

## Context
```go
ctx, cancel := context.WithTimeout(context.Background(), 2*time.Second)
defer cancel()

req, _ := http.NewRequestWithContext(ctx, "GET", url, nil)
```
