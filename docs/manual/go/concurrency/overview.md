# Concurrency Overview

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

Next: [Channels & Select](channels_select.md)
