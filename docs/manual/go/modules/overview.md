# Standard Library

Go's standard library is large and consistent. Start with core text handling, I/O, networking, and testing.

Key areas:
- Core & text: `fmt`, `strings`, `bytes`, `strconv`, `regexp`, `errors`
- I/O & files: `io`, `bufio`, `os`, `io/fs`, `filepath`, `encoding/*`
- OS & CLI: `log`, `flag`, `os/exec`
- Networking & data: `net/http`, `net`, `crypto/*`, `database/sql`

## Core and Text Packages

### fmt and strconv
Formatting and string and number conversion utilities.

```go
fmt.Printf("id=%d name=%s\n", id, name)

s := strconv.Itoa(42)
n, err := strconv.Atoi("123")
```

### strings and bytes
String and byte-slice manipulation utilities.

```go
parts := strings.Split("a,b,c", ",")
joined := strings.Join(parts, "-")

var b bytes.Buffer
b.WriteString("hello")
```

### regexp
Compile and use regular expressions.

```go
re := regexp.MustCompile(`\d+`)
nums := re.FindAllString("a1 b22 c333", -1)
```

### errors
Error creation and inspection helpers.

```go
err := fmt.Errorf("wrap: %w", base)
if errors.Is(err, base) { ... }
```

## I/O and Files

### io and bufio
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

### os, filepath, io/fs
Filesystem and path utilities in the standard library.

```go
files, _ := os.ReadDir(".")
path := filepath.Join("data", "file.txt")
```

### encoding/json
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

## OS and CLI

### log
Basic logging utilities in the standard library.

```go
log.SetFlags(log.LstdFlags | log.Lshortfile)
log.Println("started")
```

### flag
Command-line flag parsing with the standard library.

```go
port := flag.Int("port", 8080, "listen port")
flag.Parse()
```

### os/exec
Run external commands and manage processes.

```go
cmd := exec.Command("git", "status")
out, err := cmd.CombinedOutput()
```

## Networking and Data

### net/http Client
HTTP client configuration and request patterns.

```go
client := &http.Client{Timeout: 5 * time.Second}
resp, err := client.Get("https://example.com")
if err != nil {
    return err
}
defer resp.Body.Close()
```

### net/http Server
HTTP server setup and handler patterns.

```go
http.HandleFunc("/health", func(w http.ResponseWriter, r *http.Request) {
    w.WriteHeader(http.StatusOK)
    w.Write([]byte("ok"))
})
_ = http.ListenAndServe(":8080", nil)
```

### net and crypto
Networking and cryptography packages overview.

```go
ln, _ := net.Listen("tcp", ":9000")
_ = ln

buf := make([]byte, 32)
_, _ = rand.Read(buf) // crypto/rand
```

### database/sql
Core SQL database API and usage patterns.

```go
db, err := sql.Open("postgres", dsn)
if err != nil {
    return err
}
defer db.Close()

row := db.QueryRow("select id, name from users where id=$1", id)
```

Next: [Design & Architecture](../structure/index.md)
