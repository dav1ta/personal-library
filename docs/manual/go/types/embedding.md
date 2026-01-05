# Embedding and Composition

Go favors composition over inheritance. Embedding promotes fields and methods.

## Struct Embedding
```go
type Logger struct {
    *log.Logger
}

type Server struct {
    Logger
    addr string
}
```

`Server` now has access to `Logger` methods directly.

## Interface Embedding
```go
type ReadWriteCloser interface {
    io.Reader
    io.Writer
    io.Closer
}
```

## Guidelines
- Embed for reuse, not for deep type hierarchies.
- Prefer explicit fields when it improves clarity.
