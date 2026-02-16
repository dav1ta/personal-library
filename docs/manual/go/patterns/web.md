# Real-World Patterns

Practical patterns for building production Go services.

## Web Services
Practical patterns for building HTTP services with the standard library.

### Server with Timeouts
Configure an HTTP server with sensible timeouts.

```go
mux := http.NewServeMux()
mux.HandleFunc("/health", health)

srv := &http.Server{
    Addr:              ":8080",
    Handler:           mux,
    ReadHeaderTimeout: 5 * time.Second,
    ReadTimeout:       10 * time.Second,
    WriteTimeout:      10 * time.Second,
    IdleTimeout:       60 * time.Second,
}

go func() { _ = srv.ListenAndServe() }()
```

### JSON Input and Output
Decode and encode JSON safely in handlers.

```go
func decodeJSON(w http.ResponseWriter, r *http.Request, v any) error {
    r.Body = http.MaxBytesReader(w, r.Body, 1<<20) // 1 MB
    dec := json.NewDecoder(r.Body)
    dec.DisallowUnknownFields()
    return dec.Decode(v)
}

func writeJSON(w http.ResponseWriter, status int, v any) {
    w.Header().Set("Content-Type", "application/json")
    w.WriteHeader(status)
    _ = json.NewEncoder(w).Encode(v)
}
```

### Middleware Chain
Compose middleware around HTTP handlers.

```go
type Middleware func(http.Handler) http.Handler

func Chain(h http.Handler, m ...Middleware) http.Handler {
    for i := len(m) - 1; i >= 0; i-- {
        h = m[i](h)
    }
    return h
}

func Log(next http.Handler) http.Handler {
    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        start := time.Now()
        next.ServeHTTP(w, r)
        log.Printf("method=%s path=%s dur=%s", r.Method, r.URL.Path, time.Since(start))
    })
}
```

### Request Timeouts
Enforce per-request timeouts with context.

```go
func handler(w http.ResponseWriter, r *http.Request) {
    ctx, cancel := context.WithTimeout(r.Context(), 2*time.Second)
    defer cancel()

    _ = ctx
}
```

### Graceful Shutdown
Stop servers cleanly while finishing in-flight requests.

```go
stop := make(chan os.Signal, 1)
signal.Notify(stop, os.Interrupt, syscall.SIGTERM)

<-stop
ctx, cancel := context.WithTimeout(context.Background(), 10*time.Second)
defer cancel()
_ = srv.Shutdown(ctx)
```

## Data Access
Practical patterns for `database/sql` usage.

### Open and Configure Pool
Open a DB and tune pool size and lifetimes.

```go
db, err := sql.Open("postgres", dsn)
if err != nil {
    return err
}
db.SetMaxOpenConns(20)
db.SetMaxIdleConns(5)
db.SetConnMaxLifetime(30 * time.Minute)

ctx, cancel := context.WithTimeout(context.Background(), 3*time.Second)
defer cancel()
if err := db.PingContext(ctx); err != nil {
    return err
}
```

### Query Row
Fetch a single row and scan into variables.

```go
var u User
err := db.QueryRowContext(ctx, "select id, name from users where id=$1", id).
    Scan(&u.ID, &u.Name)
if errors.Is(err, sql.ErrNoRows) {
    return nil
}
if err != nil {
    return err
}
```

### Query Many Rows
Iterate over result sets and scan rows.

```go
rows, err := db.QueryContext(ctx, "select id, name from users where active=$1", true)
if err != nil {
    return err
}
defer rows.Close()

for rows.Next() {
    var u User
    if err := rows.Scan(&u.ID, &u.Name); err != nil {
        return err
    }
}
if err := rows.Err(); err != nil {
    return err
}
```

### Transactions
Use transactions for atomic database changes.

```go
tx, err := db.BeginTx(ctx, nil)
if err != nil {
    return err
}
defer tx.Rollback()

if _, err := tx.ExecContext(ctx, "update users set name=$1 where id=$2", name, id); err != nil {
    return err
}
return tx.Commit()
```

### Tips
Practical tips and gotchas.

- Always use context-aware methods.
- Close rows quickly; do not defer inside loops.
- Handle `sql.ErrNoRows` explicitly.

## Systems
Production-facing patterns for services and workers.

### Config from Environment
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

### Structured Logging
Log with fields for easier searching and analysis.

```go
logger := slog.New(slog.NewJSONHandler(os.Stdout, nil))
logger.Info("startup", "addr", addr)
```

### Graceful Worker Shutdown
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

### Retry with Backoff
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

### Bounded Work Queue
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

### Simple Rate Limiting
Basic rate limiting with tickers or tokens.

```go
tick := time.NewTicker(100 * time.Millisecond)
defer tick.Stop()

for item := range items {
    <-tick.C
    handle(item)
}
```

## Config and Secrets (5 Layers)
Use a clear precedence order so config is predictable.

### The 5 Layers (low -> high priority)
Config precedence from low to high priority sources.

1. Defaults in code
2. Config file (optional)
3. Environment variables
4. Flags / CLI overrides
5. Runtime overrides (secret stores, feature flags, etc)

Keep it boring: deterministic, validated, and easy to debug.

### Minimal Standard-Lib Loader
Small config loader using only the standard library.

```go
type Config struct {
    Addr     string
    DBURL    string
    LogLevel string
}

func LoadConfig() (Config, error) {
    cfg := Config{
        Addr:     ":8080",
        LogLevel: "info",
    }

    if v := os.Getenv("ADDR"); v != "" {
        cfg.Addr = v
    }
    if v := os.Getenv("DB_URL"); v != "" {
        cfg.DBURL = v
    }
    if v := os.Getenv("LOG_LEVEL"); v != "" {
        cfg.LogLevel = v
    }

    addr := flag.String("addr", cfg.Addr, "listen address")
    logLevel := flag.String("log-level", cfg.LogLevel, "log level")
    flag.Parse()

    cfg.Addr = *addr
    cfg.LogLevel = *logLevel

    if cfg.DBURL == "" {
        return Config{}, errors.New("DB_URL required")
    }
    return cfg, nil
}
```

### Secrets
Handle secrets securely and avoid leaking them.

- Never commit secrets to git.
- Prefer environment variables or mounted files (Kubernetes secrets).
- Separate secret values from non-secret config.

Example secret from file:

```go
secret, err := os.ReadFile("/var/run/secrets/api_key")
if err != nil {
    return err
}
apiKey := strings.TrimSpace(string(secret))
```

### When You Need a Library
If the config surface grows (multiple files, env + flags + overrides), use a config helper.

## Background Jobs
Use background jobs for slow, retryable, or non-user-facing work.

### Simple In-Process Queue
Basic in-memory queue for background work.

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

### Retry with Backoff + Jitter
Add jitter to backoff to avoid synchronized retries.

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

### Scheduled Jobs
Run periodic jobs with tickers or schedulers.

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

### Real-World Rules
Operational rules that keep background jobs reliable.

- Make jobs idempotent; retries happen.
- Add deduplication keys for long-running tasks.
- If you need durability, use a persistent queue (Redis, DB, Kafka).
- Use a dead-letter queue for poison jobs.

## Common Libraries
Practical, commonly used choices. Prefer the standard library unless you need more.

### HTTP APIs and Routing
Libraries and patterns for building HTTP APIs and routing.

- net/http (stdlib)
- chi (router + middleware)
- gin, echo (web frameworks)
- fiber (web framework on fasthttp)

### WebSockets
Libraries and patterns for WebSocket support.

- gorilla/websocket
- coder/websocket
- gobwas/ws (low-level)

### Database and SQL
Libraries and patterns for database access and SQL.

- database/sql (stdlib)
- pgx (Postgres driver + toolkit)
- go-redis (Redis client)

### Migrations
Schema migration tools and workflows.

- golang-migrate
- goose

### Background Jobs / Workflows
Libraries and patterns for background job processing and workflows.

- asynq (Redis-backed jobs)
- Temporal (workflow engine)

### Config
Config libraries and patterns commonly used in Go services.

- viper
- koanf
- envconfig
- godotenv (load .env in dev)

### Logging and Observability
Options for logging, metrics, and tracing.

- slog (stdlib)
- zerolog
- OpenTelemetry Go

### Messaging / Eventing
Libraries and patterns for messaging and eventing.

- Watermill

Next: [Standard Library](../modules/overview.md)
