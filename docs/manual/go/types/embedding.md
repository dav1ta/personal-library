# Embedding and Composition

Go favors composition over inheritance. Embedding promotes fields and methods.

## Struct Embedding
Compose structs by embedding fields.

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
Compose interfaces by embedding smaller ones.

```go
type ReadWriteCloser interface {
    io.Reader
    io.Writer
    io.Closer
}
```

## Guidelines
Practical do and do not guidance for structure.

- Embed for reuse, not for deep type hierarchies.
- Prefer explicit fields when it improves clarity.

Next: [Overview](../concurrency/overview.md)
