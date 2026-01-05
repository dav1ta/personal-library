# Practices

- Use `gofmt` and `go vet` as defaults.
- Check every error return; be explicit.
- Keep packages small and cohesive.
- Prefer composition and interfaces over inheritance.
- Pass `context.Context` as the first parameter when needed.
- Avoid global state; inject dependencies.
- Use channels for ownership transfer, mutexes for shared state.
