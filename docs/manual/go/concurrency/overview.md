# Concurrency Overview

Goroutines are lightweight threads managed by the Go runtime.

## Start a Goroutine
```go
go func() {
    work()
}()
```

## Wait for Work
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
- Concurrency is about structure, not parallelism.
- Always have a shutdown path to avoid goroutine leaks.
