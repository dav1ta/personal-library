# I/O and Files

## io and bufio
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
```go
files, _ := os.ReadDir(".")
path := filepath.Join("data", "file.txt")
```

## encoding/json
```go
type User struct {
    ID   int    `json:"id"`
    Name string `json:"name"`
}

data, _ := json.Marshal(User{ID: 1, Name: "Ada"})
var u User
_ = json.Unmarshal(data, &u)
```
