# Channels and Select

Channels synchronize goroutines and transfer data.

## Unbuffered and Buffered
```go
ch := make(chan int)      // unbuffered
buf := make(chan int, 10) // buffered
```

## Send, Receive, Close
```go
ch <- 1
v := <-ch
close(ch)

for v := range ch {
    _ = v
}
```

## Select
```go
select {
case v := <-ch:
    _ = v
case <-time.After(2 * time.Second):
    return errors.New("timeout")
default:
    // non-blocking path
}
```
