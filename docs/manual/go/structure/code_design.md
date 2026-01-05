# Code Design

## Keep APIs Small
Expose only what you need. A small surface area is easier to maintain.

## Accept Interfaces, Return Concrete Types
```go
func NewStore(db *sql.DB) *Store { ... }
func (s *Store) List(ctx context.Context) ([]Item, error) { ... }
```

## Functional Options
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

## Error Boundaries
Handle errors at boundaries. Keep inner layers pure and simple.
