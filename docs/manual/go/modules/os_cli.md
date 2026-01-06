# OS and CLI

## log
Basic logging utilities in the standard library.

```go
log.SetFlags(log.LstdFlags | log.Lshortfile)
log.Println("started")
```

## flag
Command-line flag parsing with the standard library.

```go
port := flag.Int("port", 8080, "listen port")
flag.Parse()
```

## os/exec
Run external commands and manage processes.

```go
cmd := exec.Command("git", "status")
out, err := cmd.CombinedOutput()
```

Next: [Networking & Data](net_data.md)
