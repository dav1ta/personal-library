# Environment and Modules

Go modules are the default dependency system. The module root contains `go.mod`.

## Module Basics
Module paths, versions, and go.mod basics.

```bash
go mod init example.com/app
go mod tidy
```

## Common Commands
Frequently used go tool commands and what they do.

```bash
go list ./...
go test ./...
go mod download
```

## Environment
Environment variables that affect the Go toolchain.

```bash
go env GOPATH
go env GOMOD
go env GOCACHE
```

Notes:
- `GOPATH` is still used for caches.
- Modules work outside `GOPATH` by default.

Next: [Structs & Methods](../types/structs_methods.md)
