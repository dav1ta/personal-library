# Unsafe

The `unsafe` package bypasses Go's type safety. Use only when you must.

## Size and Alignment
```go
size := unsafe.Sizeof(int64(0))
align := unsafe.Alignof(int64(0))
```

## Pointer Conversions
```go
var x int64 = 1
p := unsafe.Pointer(&x)
q := (*int64)(p)
```

Guidelines:
- Avoid `unsafe` unless you have measured a real benefit.
- Prefer well-tested library code over custom unsafe hacks.
