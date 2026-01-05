# OS and CLI

## log
```go
log.SetFlags(log.LstdFlags | log.Lshortfile)
log.Println("started")
```

## flag
```go
port := flag.Int("port", 8080, "listen port")
flag.Parse()
```

## os/exec
```go
cmd := exec.Command("git", "status")
out, err := cmd.CombinedOutput()
```
