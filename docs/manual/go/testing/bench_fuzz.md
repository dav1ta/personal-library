# Benchmarks and Fuzzing

## Benchmarks
Measure performance with testing.B benchmarks.

```go
func BenchmarkEncode(b *testing.B) {
    for i := 0; i < b.N; i++ {
        _ = encode(data)
    }
}
```

Useful helpers:
```go
func BenchmarkParse(b *testing.B) {
    b.ReportAllocs()
    b.ResetTimer()
    for i := 0; i < b.N; i++ {
        _ = parse(input)
    }
}
```

Run:
```bash
go test -bench . -benchmem
```

## Fuzzing
Coverage-guided tests to find edge cases.

```go
func FuzzParse(f *testing.F) {
    f.Add("1")
    f.Add("2")

    f.Fuzz(func(t *testing.T, in string) {
        _, _ = parse(in)
    })
}
```

Run:
```bash
go test -fuzz=FuzzParse
```

Next: [Test Patterns](test_patterns.md)
