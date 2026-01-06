# I/O and Files

## io and bufio
I/O primitives and buffered I/O helpers.

```go
src, _ := os.Open("in.txt")
defer src.Close()

dst, _ := os.Create("out.txt")
defer dst.Close()

_, _ = io.Copy(dst, src)
```

```go
r := bufio.NewReader(src)
line, _ := r.ReadString('\n')
```

## os, filepath, io/fs
Filesystem and path utilities in the standard library.

```go
files, _ := os.ReadDir(".")
path := filepath.Join("data", "file.txt")
```

## encoding/json
JSON encoding and decoding in the standard library.

```go
type User struct {
    ID   int    `json:"id"`
    Name string `json:"name"`
}

data, _ := json.Marshal(User{ID: 1, Name: "Ada"})
var u User
_ = json.Unmarshal(data, &u)
```

Next: [OS & CLI](os_cli.md)
