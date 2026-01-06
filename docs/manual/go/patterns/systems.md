# Systems Patterns

Production-facing patterns for services and workers.

## Config from Environment
```go
func env(key, def string) string {
    if v := os.Getenv(key); v != "" {
        return v
    }
    return def
}

addr := env("ADDR", ":8080")
```

## Structured Logging
```go
logger := slog.New(slog.NewJSONHandler(os.Stdout, nil))
logger.Info("startup", "addr", addr)
```

## Graceful Worker Shutdown
```go
ctx, stop := signal.NotifyContext(context.Background(), os.Interrupt, syscall.SIGTERM)
defer stop()

var wg sync.WaitGroup
wg.Add(1)
go func() {
    defer wg.Done()
    for {
        select {
        case <-ctx.Done():
            return
        default:
            work()
        }
    }
}()
wg.Wait()
```

## Retry with Backoff
```go
func Retry(ctx context.Context, attempts int, base time.Duration, fn func() error) error {
    var err error
    for i := 0; i < attempts; i++ {
        if err = fn(); err == nil {
            return nil
        }
        wait := base * time.Duration(1<<i)
        t := time.NewTimer(wait)
        select {
        case <-ctx.Done():
            t.Stop()
            return ctx.Err()
        case <-t.C:
        }
    }
    return err
}
```

## Bounded Work Queue
```go
jobs := make(chan Job, 100)

for i := 0; i < 4; i++ {
    go func() {
        for j := range jobs {
            handle(j)
        }
    }()
}
```

## Simple Rate Limiting
```go
tick := time.NewTicker(100 * time.Millisecond)
defer tick.Stop()

for item := range items {
    <-tick.C
    handle(item)
}
```
