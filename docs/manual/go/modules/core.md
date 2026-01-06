# Core and Text Packages

## fmt and strconv
Formatting and string and number conversion utilities.

```go
fmt.Printf("id=%d name=%s\n", id, name)

s := strconv.Itoa(42)
n, err := strconv.Atoi("123")
```

## strings and bytes
String and byte-slice manipulation utilities.

```go
parts := strings.Split("a,b,c", ",")
joined := strings.Join(parts, "-")

var b bytes.Buffer
b.WriteString("hello")
```

## regexp
Compile and use regular expressions.

```go
re := regexp.MustCompile(`\d+`)
nums := re.FindAllString("a1 b22 c333", -1)
```

## errors
Error creation and inspection helpers.

```go
err := fmt.Errorf("wrap: %w", base)
if errors.Is(err, base) { ... }
```

Next: [I/O & Files](io_files.md)
