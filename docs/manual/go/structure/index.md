# Design and Architecture

Go design favors clarity, simple types, and small interfaces. Keep APIs minimal and avoid over-engineering.

## Code Design

### Keep APIs Small
Expose only what you need. A small surface area is easier to maintain.

### Accept Interfaces, Return Concrete Types
Design APIs to accept interfaces for flexibility while returning concrete types for clarity.

```go
func NewStore(db *sql.DB) *Store { ... }
func (s *Store) List(ctx context.Context) ([]Item, error) { ... }
```

### Functional Options
Option functions for flexible configuration APIs.

```go
type Client struct {
    timeout time.Duration
}

type Option func(*Client)

func WithTimeout(d time.Duration) Option {
    return func(c *Client) { c.timeout = d }
}

func NewClient(opts ...Option) *Client {
    c := &Client{timeout: 5 * time.Second}
    for _, opt := range opts {
        opt(c)
    }
    return c
}
```

### Error Boundaries
Handle errors at boundaries. Keep inner layers pure and simple.

## Practices

- Use `gofmt` and `go vet` as defaults.
- Check every error return; be explicit.
- Keep packages small and cohesive.
- Prefer composition and interfaces over inheritance.
- Pass `context.Context` as the first parameter when needed.
- Avoid global state; inject dependencies.
- Use channels for ownership transfer, mutexes for shared state.

## Project Layout
There is no official layout, but these conventions are common:

```
myapp/
  cmd/           # entry points
  internal/      # private packages
  pkg/           # public packages (optional)
  api/           # API definitions
  configs/
  scripts/
  testdata/      # test fixtures
```

Guidelines:
- Keep `main` small; push logic into packages.
- Keep package boundaries clear and stable.

Next: [Testing](../testing/testing.md)
