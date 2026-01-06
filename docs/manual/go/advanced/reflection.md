# Reflection

Reflection lets you inspect types at runtime. Use sparingly.

## Inspect Types
Use reflection to inspect types and values at runtime.

```go
t := reflect.TypeOf(42)
fmt.Println(t.Kind()) // int
```

## Read Struct Tags
Access and interpret struct tags via reflection.

```go
type User struct {
    ID   int    `json:"id"`
    Name string `json:"name"`
}

field, _ := reflect.TypeOf(User{}).FieldByName("Name")
tag := field.Tag.Get("json") // "name"
```

## Set Values
Set values via reflection safely.

```go
v := reflect.ValueOf(&x).Elem()
v.SetInt(10)
```

Notes:
- Reflection is slower and less safe than static code.
- Prefer interfaces when possible.

Next: [Unsafe](unsafe.md)
