# Errors

Go uses explicit error returns. The built-in interface is:

```go
type error interface {
    Error() string
}
```

## Creating Errors
Create errors with context and useful messages.

```go
err := errors.New("bad input")
err = fmt.Errorf("bad input: %s", msg)
```

## Wrapping and Unwrapping
Add context to errors and unwrap causes.

```go
if err != nil {
    return fmt.Errorf("load config: %w", err)
}

if errors.Is(err, os.ErrNotExist) { ... }
var pe *os.PathError
if errors.As(err, &pe) { ... }
```

## Sentinel Errors
Use shared error values for comparisons.

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
Define error types to carry structured details.

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

Next: [Builtins](builtins.md)
