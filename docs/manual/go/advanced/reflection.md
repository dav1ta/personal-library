# Reflection

Reflection lets you inspect types at runtime. Use sparingly.

## Inspect Types
```go
t := reflect.TypeOf(42)
fmt.Println(t.Kind()) // int
```

## Read Struct Tags
```go
type User struct {
    ID   int    `json:"id"`
    Name string `json:"name"`
}

field, _ := reflect.TypeOf(User{}).FieldByName("Name")
tag := field.Tag.Get("json") // "name"
```

## Set Values
```go
v := reflect.ValueOf(&x).Elem()
v.SetInt(10)
```

Notes:
- Reflection is slower and less safe than static code.
- Prefer interfaces when possible.
