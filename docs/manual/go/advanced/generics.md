# Advanced Go Topics

Deep dives into generics, reflection, unsafe, and memory/tooling.

## Generics
Go supports type parameters for functions and types.

### Generic Function
A function that works over multiple types using type parameters.

```go
func Map[T any, U any](in []T, f func(T) U) []U {
    out := make([]U, len(in))
    for i, v := range in {
        out[i] = f(v)
    }
    return out
}
```

### Constraints
Restrict type parameters to a set of allowed operations.

```go
type Set[T comparable] map[T]struct{}

type Number interface {
    ~int | ~int64 | ~float64
}
```

### When to Use
Guidance on when this approach is appropriate.

- Use generics for reusable algorithms and data structures.
- Avoid generics when a simple interface or concrete type is clearer.

## Reflection
Reflection lets you inspect types at runtime. Use sparingly.

### Inspect Types
Use reflection to inspect types and values at runtime.

```go
t := reflect.TypeOf(42)
fmt.Println(t.Kind()) // int
```

### Read Struct Tags
Access and interpret struct tags via reflection.

```go
type User struct {
    ID   int    `json:"id"`
    Name string `json:"name"`
}

field, _ := reflect.TypeOf(User{}).FieldByName("Name")
tag := field.Tag.Get("json") // "name"
```

### Set Values
Set values via reflection safely.

```go
v := reflect.ValueOf(&x).Elem()
v.SetInt(10)
```

Notes:
- Reflection is slower and less safe than static code.
- Prefer interfaces when possible.

## Unsafe
The `unsafe` package bypasses Go's type safety. Use only when you must.

### Size and Alignment
How size and alignment affect memory layout.

```go
size := unsafe.Sizeof(int64(0))
align := unsafe.Alignof(int64(0))
```

### Pointer Conversions
Unsafe pointer conversions and the risks involved.

```go
var x int64 = 1
p := unsafe.Pointer(&x)
q := (*int64)(p)
```

Guidelines:
- Avoid `unsafe` unless you have measured a real benefit.
- Prefer well-tested library code over custom unsafe hacks.

## Memory and Tooling

### GC and Escape Analysis
Go uses a concurrent garbage collector. Escape analysis decides whether values live on the heap.

Tips:
- Prefer value types when small.
- Avoid capturing large objects in closures.
- Reuse buffers in hot paths.

### Profiling with pprof
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

### Build and Tooling
Build, test, and tooling commands for Go projects.

- `gofmt` for formatting
- `go vet` for static checks
- `go test` for tests and benchmarks

Next: [Go Overview](../index.md)
