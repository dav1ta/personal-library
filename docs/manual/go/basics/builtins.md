# Built-in Functions

Common built-ins you will use frequently:

- `len` / `cap` for strings, arrays, slices, maps, channels
- `make` to initialize slices, maps, channels
- `new` to allocate zeroed values
- `append` to grow slices
- `copy` to copy slices
- `delete` to remove map entries
- `complex`, `real`, `imag` for complex numbers
- `close` to close channels
- `panic` / `recover` for unrecoverable errors

## Examples
```go
s := make([]int, 0, 10)
s = append(s, 1, 2, 3)

dst := make([]int, len(s))
copy(dst, s)

m := map[string]int{"a": 1}
delete(m, "a")
```
