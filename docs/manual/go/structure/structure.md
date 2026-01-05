# Project Layout

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
