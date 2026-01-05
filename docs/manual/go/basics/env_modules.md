# Environment and Modules

Go modules are the default dependency system. The module root contains `go.mod`.

## Module Basics
```bash
go mod init example.com/app
go mod tidy
```

## Common Commands
```bash
go list ./...
go test ./...
go mod download
```

## Environment
```bash
go env GOPATH
go env GOMOD
go env GOCACHE
```

Notes:
- `GOPATH` is still used for caches.
- Modules work outside `GOPATH` by default.
