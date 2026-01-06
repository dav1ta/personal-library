# Systems Patterns

Production-facing patterns for services and workers.

## Config from Environment
Load configuration from environment variables with validation.

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
Log with fields for easier searching and analysis.

```go
logger := slog.New(slog.NewJSONHandler(os.Stdout, nil))
logger.Info("startup", "addr", addr)
```

## Graceful Worker Shutdown
Stop workers safely without losing queued work.

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
Retry failures with increasing delays.

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
Limit queued work to protect memory and smooth load.

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
Basic rate limiting with tickers or tokens.

```go
tick := time.NewTicker(100 * time.Millisecond)
defer tick.Stop()

for item := range items {
    <-tick.C
    handle(item)
}
```

Next: [Config and Secrets](config.md)
