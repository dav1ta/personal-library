# Structs

Structs group related fields into a named type.

## Definition and Literals
```go
type User struct {
    ID   int
    Name string
}

u := User{ID: 1, Name: "Ada"}
v := User{1, "Linus"} // positional
```

## Zero Values
```go
var u User
fmt.Println(u.ID)   // 0
fmt.Println(u.Name) // ""
```

## Pointers to Structs
```go
u := &User{ID: 1}
u.Name = "Ada" // automatic dereference
```

## Struct Tags
```go
type Person struct {
    Name string `json:"name"`
    Age  int    `json:"age,omitempty"`
}
```

## Comparability
Structs are comparable if all fields are comparable.

```go
type Point struct{ X, Y int }
fmt.Println(Point{1, 2} == Point{1, 2}) // true
```

For non-comparable fields (slices, maps), use a custom equality function.
