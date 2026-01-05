# Project Structure

Keep packages small and focused. Avoid deep nesting unless it adds clarity.

## Common Layout
```
myapp/
  go.mod
  cmd/
    api/
      main.go
  internal/
    store/
    service/
  pkg/          # optional, for reusable packages
  assets/
  scripts/
```

## Guidelines
- One package per folder.
- Export only what you intend to support.
- Prefer composition over inheritance.
- Keep `main` packages thin; push logic into internal packages.
