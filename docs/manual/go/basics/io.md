# I/O Basics

Go centers I/O around small interfaces like `io.Reader` and `io.Writer`.

## Read a File
```go
data, err := os.ReadFile("config.json")
if err != nil {
    return err
}
```

## Stream Copy
```go
src, _ := os.Open("in.txt")
dst, _ := os.Create("out.txt")
defer src.Close()
defer dst.Close()

_, err := io.Copy(dst, src)
```

## Buffered I/O
```go
f, _ := os.Open("app.log")
defer f.Close()

scanner := bufio.NewScanner(f)
for scanner.Scan() {
    line := scanner.Text()
    _ = line
}
if err := scanner.Err(); err != nil {
    return err
}
```
