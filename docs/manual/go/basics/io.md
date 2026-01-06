# I/O Basics

Go centers I/O around small interfaces like `io.Reader` and `io.Writer`.

## Read a File
Read a file into memory with proper error handling.

```go
data, err := os.ReadFile("config.json")
if err != nil {
    return err
}
```

## Stream Copy
Copy streams efficiently between readers and writers.

```go
src, _ := os.Open("in.txt")
dst, _ := os.Create("out.txt")
defer src.Close()
defer dst.Close()

_, err := io.Copy(dst, src)
```

## Buffered I/O
Use buffered readers and writers for efficient streaming.

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

Next: [Pointers](pointers.md)
