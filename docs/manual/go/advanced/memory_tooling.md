# Memory and Tooling

## GC and Escape Analysis
Go uses a concurrent garbage collector. Escape analysis decides whether values live on the heap.

Tips:
- Prefer value types when small.
- Avoid capturing large objects in closures.
- Reuse buffers in hot paths.

## Profiling with pprof
Profile CPU and memory usage with pprof.

```go
import _ "net/http/pprof"

go func() {
    _ = http.ListenAndServe("localhost:6060", nil)
}()
```

Then:
```bash
go tool pprof http://localhost:6060/debug/pprof/heap
```

## Build and Tooling
Build, test, and tooling commands for Go projects.

- `gofmt` for formatting
- `go vet` for static checks
- `go test` for tests and benchmarks

Next: [Overview](../index.md)
