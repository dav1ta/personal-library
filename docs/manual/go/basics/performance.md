# Performance Basics

Measure first. Optimize after you have a clear bottleneck.

## Reduce Allocations
```go
buf := make([]byte, 0, 1024)
buf = append(buf, data...)
```

Use `strings.Builder` or `bytes.Buffer` for concatenation.

## Avoid Unnecessary Conversions
```go
// avoid []byte -> string -> []byte roundtrips in hot paths
```

## Reuse Objects
```go
var pool = sync.Pool{
    New: func() any { return make([]byte, 0, 4096) },
}
```

## Profile with Benchmarks
```bash
go test -bench . -benchmem
```

If code is latency-sensitive, add pprof and measure.
