# Packages and Modules

A package is a directory of Go files with the same `package` name. A module is a versioned collection of packages defined by `go.mod`.

## Imports
```go
package main

import (
    "fmt"
    "net/http"
)
```

## Exported Identifiers
Names starting with a capital letter are exported.

```go
type Client struct{}
func NewClient() *Client { return &Client{} }
```

## init
```go
func init() {
    // runs before main, avoid heavy work here
}
```

## Module Basics
```bash
go mod init example.com/app
go mod tidy
```

## internal Packages
Code under `internal/` is importable only from within the module.
