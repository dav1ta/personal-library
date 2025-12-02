# Rust for Python Developers: A Comparative Guide with Advanced Examples

## Variables and Data Types

### Python
- **Dynamic Typing**: Variables can change types.
- **Types**: `int`, `float`, `str`, `bool`, complex, `NoneType`.
- **Collections**: `list`, `tuple`, `dict`, `set`.

```python
x = 100  # Integer
x = "Hello, World!"  # Now a string
data = {'key': 'value', 'number': 42}
```

### Rust
- **Static Typing**: Variable types are known at compile time.
- **Scalar Types**: `i32`, `f64`, `bool`, `char`.
- **Compound Types**: Tuples, Arrays.
- **Mutability**: Variables are immutable by default. Use `mut` for mutability.
- **Ownership and Borrowing**: Core features for memory safety.

```rust
let x: i32 = 100;
let mut y = "Hello"; // mutable
y = "World!";
let data: HashMap<&str, i32> = [("key", 42)].iter().cloned().collect();
```

## Control Flow

### Python
- **Loops**: `for`, `while`.
- **Conditional Statements**: `if`, `elif`, `else`.

```python
for i in range(5):
    print(i)

if x > 0:
    print("Positive")
elif x == 0:
    print("Zero")
else:
    print("Negative")
```

### Rust
- **Loops**: `loop`, `while`, `for`.
- **Conditional Statements**: `if`, `else`, `match` for pattern matching.

```rust
for i in 0..5 {
    println!("{}", i);
}

match x {
    0 => println!("Zero"),
    _ if x > 0 => println!("Positive"),
    _ => println!("Negative"),
}
```

## Functions and Methods

### Python
- **Defining Functions**: Use `def`.
- **Parameters & Return Types**: Dynamically typed.
- **First-Class Objects**: Functions can be passed around.

```python
def add(a, b):
    return a + b

class MyClass:
    def method(self):
        print("Method called")
```

### Rust
- **Defining Functions**: Use `fn`, specify types.
- **Parameters & Return Types**: Statically typed.
- **First-Class Objects**: Functions can be variables or arguments.

```rust
fn add(a: i32, b: i32) -> i32 {
    a + b
}

struct MyClass;

impl MyClass {
    fn method(&self) {
        println!("Method called");
    }
}
```

## Error Handling

### Python
- **Exceptions**: Use `try`, `except`, `finally`.

```python
try:
    result = risky_operation()
except Exception as e:
    print(f"Error: {e}")
finally:
    clean_up()
```

### Rust
- **Result and Option**: No exceptions, use `Result<T, E>`, `Option<T>` for error handling.

```rust
fn risky_operation() -> Result<i32, &'static str> {
    if success {
        Ok(42)
    } else {
        Err("Failed")
    }
}

match risky_operation() {
    Ok(n) => println!("Success: {}", n),
    Err(e) => println!("Error: {}", e),
}
```

## Collections

### Python
- Lists, dictionaries, and sets with dynamic typing and various methods.

```python
my_list = [1, 2, 3]
my_dict = {"key": "value"}
my_set = {1, 2, 3}
```

### Rust
- Vectors, hash maps, and sets with static typing and safety.

```rust
let my_vec = vec![1, 2, 3];
let mut my_map: HashMap<&str, &str> = HashMap::new();
my_map.insert("key", "value");
let my_set: HashSet<i32> = [1, 2, 3].iter().cloned().collect();
```

This structure outlines a comprehensive, example-driven comparison between Python and Rust, focusing on advanced aspects of each topic. Proceed by fleshing out each section with detailed examples and explanations.
