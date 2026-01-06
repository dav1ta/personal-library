# Performance Basics

Measure first. Optimize after you have a clear bottleneck.

## Reduce Allocations
Techniques to lower heap allocations.

```go
buf := make([]byte, 0, 1024)
buf = append(buf, data...)
```

Use `strings.Builder` or `bytes.Buffer` for concatenation.

## Avoid Unnecessary Conversions
Minimize type conversions to keep code clear and reduce overhead.

```go
// avoid []byte -> string -> []byte roundtrips in hot paths
```

## Reuse Objects
Reuse buffers and objects to reduce GC pressure.

```go
var pool = sync.Pool{
    New: func() any { return make([]byte, 0, 4096) },
}
```

## Profile with Benchmarks
Use benchmarks to measure changes over time.

```bash
go test -bench . -benchmem
```

If code is latency-sensitive, add pprof and measure.

Next: [Environment & Modules](env_modules.md)
