# Background Jobs

Use background jobs for slow, retryable, or non-user-facing work.

## Simple In-Process Queue
```go
type Job func(context.Context) error

func StartWorkers(ctx context.Context, n int, jobs <-chan Job) *sync.WaitGroup {
    var wg sync.WaitGroup
    wg.Add(n)
    for i := 0; i < n; i++ {
        go func() {
            defer wg.Done()
            for {
                select {
                case <-ctx.Done():
                    return
                case job, ok := <-jobs:
                    if !ok {
                        return
                    }
                    _ = job(ctx)
                }
            }
        }()
    }
    return &wg
}
```

## Retry with Backoff + Jitter
```go
func Retry(ctx context.Context, attempts int, base time.Duration, fn func() error) error {
    var err error
    for i := 0; i < attempts; i++ {
        if err = fn(); err == nil {
            return nil
        }
        jitter := time.Duration(rand.Int63n(int64(base)))
        wait := base*time.Duration(1<<i) + jitter
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

## Scheduled Jobs
```go
ticker := time.NewTicker(5 * time.Minute)
defer ticker.Stop()

for {
    select {
    case <-ctx.Done():
        return
    case <-ticker.C:
        _ = runJob(ctx)
    }
}
```

## Real-World Rules
- Make jobs idempotent; retries happen.
- Add deduplication keys for long-running tasks.
- If you need durability, use a persistent queue (Redis, DB, Kafka).
- Use a dead-letter queue for poison jobs.

For durable jobs, see [Common Libraries](libraries.md).
