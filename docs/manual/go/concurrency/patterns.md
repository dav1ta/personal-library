# Concurrency Patterns

## Worker Pool
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
```go
stage1 := func(in <-chan int) <-chan int { ... }
stage2 := func(in <-chan int) <-chan int { ... }
```

## Rate Limiting
```go
ticker := time.NewTicker(100 * time.Millisecond)
defer ticker.Stop()

for item := range items {
    <-ticker.C
    handle(item)
}
```
