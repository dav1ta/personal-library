# Concurrency Patterns

## Worker Pool
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

## Fan-out / Fan-in
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

## Pipeline
Connect stages with channels for streaming work.

```go
stage1 := func(in <-chan int) <-chan int { ... }
stage2 := func(in <-chan int) <-chan int { ... }
```

## Rate Limiting
Limit request or job throughput to protect services.

```go
ticker := time.NewTicker(100 * time.Millisecond)
defer ticker.Stop()

for item := range items {
    <-ticker.C
    handle(item)
}
```

Next: [Web Services](../patterns/web.md)
