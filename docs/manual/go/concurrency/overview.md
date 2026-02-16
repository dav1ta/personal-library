# Concurrency

Goroutines are lightweight threads managed by the Go runtime.

## Start a Goroutine
Launch concurrent work with the go keyword.

```go
go func() {
    work()
}()
```

## Wait for Work
Coordinate goroutines to wait for completion.

```go
var wg sync.WaitGroup
wg.Add(1)
go func() {
    defer wg.Done()
    work()
}()
wg.Wait()
```

## Key Ideas
The core mental models to keep in mind.

- Concurrency is about structure, not parallelism.
- Always have a shutdown path to avoid goroutine leaks.

## Channels and Select
Channels synchronize goroutines and transfer data.

### Unbuffered and Buffered
How channel buffering changes synchronization.

```go
ch := make(chan int)      // unbuffered
buf := make(chan int, 10) // buffered
```

### Send, Receive, Close
Channel send, receive, and close semantics.

```go
ch <- 1
v := <-ch
close(ch)

for v := range ch {
    _ = v
}
```

### Select
Wait on multiple channel operations.

```go
select {
case v := <-ch:
    _ = v
case <-time.After(2 * time.Second):
    return errors.New("timeout")
default:
    // non-blocking path
}
```

## Sync and Context
Use `sync` primitives for shared memory. Use `context` for cancellation and timeouts.

### Mutex and RWMutex
Protect shared data with mutual exclusion locks.

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

### Once and Pool
One-time initialization and object pooling patterns.

```go
var once sync.Once
once.Do(initConfig)

var pool = sync.Pool{New: func() any { return make([]byte, 0, 4096) }}
```

### Context
Pass cancellation, deadlines, and request-scoped values.

```go
ctx, cancel := context.WithTimeout(context.Background(), 2*time.Second)
defer cancel()

req, _ := http.NewRequestWithContext(ctx, "GET", url, nil)
```

## Concurrency Patterns
Common patterns for structuring concurrent work.

### Worker Pool
Bounded concurrency with a pool of workers.

```go
jobs := make(chan Job)
results := make(chan Result)

for i := 0; i < 4; i++ {
    go func() {
        for j := range jobs {
            results <- handle(j)
        }
    }()
}
```

### Fan-out / Fan-in
Spread work across goroutines and merge results.

```go
in := make(chan Item)
out := make(chan Item)

for i := 0; i < 4; i++ {
    go func() {
        for v := range in {
            out <- process(v)
        }
    }()
}
```

### Pipeline
Connect stages with channels for streaming work.

```go
stage1 := func(in <-chan int) <-chan int { ... }
stage2 := func(in <-chan int) <-chan int { ... }
```

### Rate Limiting
Limit request or job throughput to protect services.

```go
ticker := time.NewTicker(100 * time.Millisecond)
defer ticker.Stop()

for item := range items {
    <-ticker.C
    handle(item)
}
```

Next: [Real-World Patterns](../patterns/web.md)
