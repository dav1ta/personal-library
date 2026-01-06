# Unsafe

The `unsafe` package bypasses Go's type safety. Use only when you must.

## Size and Alignment
How size and alignment affect memory layout.

```go
size := unsafe.Sizeof(int64(0))
align := unsafe.Alignof(int64(0))
```

## Pointer Conversions
Unsafe pointer conversions and the risks involved.

```go
var x int64 = 1
p := unsafe.Pointer(&x)
q := (*int64)(p)
```

Guidelines:
- Avoid `unsafe` unless you have measured a real benefit.
- Prefer well-tested library code over custom unsafe hacks.

Next: [Memory & Tooling](memory_tooling.md)
