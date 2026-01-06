# Channels and Select

Channels synchronize goroutines and transfer data.

## Unbuffered and Buffered
How channel buffering changes synchronization.

```go
ch := make(chan int)      // unbuffered
buf := make(chan int, 10) // buffered
```

## Send, Receive, Close
Channel send, receive, and close semantics.

```go
ch <- 1
v := <-ch
close(ch)

for v := range ch {
    _ = v
}
```

## Select
Wait on multiple channel operations.

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

Next: [Sync + Context](sync_context.md)
