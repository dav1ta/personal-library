# Web Service Patterns

Practical patterns for building HTTP services with the standard library.

## Server with Timeouts
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

## JSON Input and Output
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

## Middleware Chain
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

## Request Timeouts
Enforce per-request timeouts with context.

```go
func handler(w http.ResponseWriter, r *http.Request) {
    ctx, cancel := context.WithTimeout(r.Context(), 2*time.Second)
    defer cancel()

    _ = ctx
}
```

## Graceful Shutdown
Stop servers cleanly while finishing in-flight requests.

```go
stop := make(chan os.Signal, 1)
signal.Notify(stop, os.Interrupt, syscall.SIGTERM)

<-stop
ctx, cancel := context.WithTimeout(context.Background(), 10*time.Second)
defer cancel()
_ = srv.Shutdown(ctx)
```

Next: [Data Access](data.md)
