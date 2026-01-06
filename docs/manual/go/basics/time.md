# Time

Go uses the `time` package for timestamps, durations, and timers.

## Time and Duration
Work with time.Time, time.Duration, and arithmetic.

```go
now := time.Now()
deadline := now.Add(2 * time.Hour)

var d time.Duration = 1500 * time.Millisecond
fmt.Println(d.Seconds())
```

## Parse and Format
Go uses a reference layout: `2006-01-02 15:04:05`.

```go
layout := "2006-01-02"
t, err := time.Parse(layout, "2025-12-01")
out := t.Format(time.RFC3339)
```

## Timeouts
Apply timeouts to operations and requests.

```go
ctx, cancel := context.WithTimeout(context.Background(), 2*time.Second)
defer cancel()
```

Next: [Project Structure](structure.md)
