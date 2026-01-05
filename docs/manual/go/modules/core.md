# Core and Text Packages

## fmt and strconv
```go
fmt.Printf("id=%d name=%s\n", id, name)

s := strconv.Itoa(42)
n, err := strconv.Atoi("123")
```

## strings and bytes
```go
parts := strings.Split("a,b,c", ",")
joined := strings.Join(parts, "-")

var b bytes.Buffer
b.WriteString("hello")
```

## regexp
```go
re := regexp.MustCompile(`\d+`)
nums := re.FindAllString("a1 b22 c333", -1)
```

## errors
```go
err := fmt.Errorf("wrap: %w", base)
if errors.Is(err, base) { ... }
```
