# Errors

Go uses explicit error returns. The built-in interface is:

```go
type error interface {
    Error() string
}
```

## Creating Errors
```go
err := errors.New("bad input")
err = fmt.Errorf("bad input: %s", msg)
```

## Wrapping and Unwrapping
```go
if err != nil {
    return fmt.Errorf("load config: %w", err)
}

if errors.Is(err, os.ErrNotExist) { ... }
var pe *os.PathError
if errors.As(err, &pe) { ... }
```

## Sentinel Errors
```go
var ErrNotFound = errors.New("not found")

func Lookup(id string) (Item, error) {
    if id == "" {
        return Item{}, ErrNotFound
    }
    ...
}
```

## Custom Error Types
```go
type ValidationError struct {
    Field string
    Msg   string
}

func (e ValidationError) Error() string {
    return e.Field + ": " + e.Msg
}
```

## Panic and Recover
Use panic for programmer errors or impossible states, not expected failures.
