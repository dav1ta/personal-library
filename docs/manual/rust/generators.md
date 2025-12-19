# Data, Traits, Generics, and Iterators

**Overview:** See the consolidated navigation in [Rust Docs Overview](index.md).

## Table of Contents
- Structs and Enums
- Methods and Associated Functions
- Traits and Trait Objects
- Generics and Trait Bounds
- Lifetimes in Practice
- Collections and Ownership Patterns
- Iterators and Functional Style
- Error Handling Patterns

## Structs and Enums
- Structs group named fields; tuples structs are positional; unit structs carry no data:
```rust
struct User { id: u64, email: String, active: bool }
struct Point(f64, f64);
struct Marker;
```
- Enums model variants with or without payloads:
```rust
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(u8, u8, u8),
}
```
- Use enums plus `match` to represent state machines cleanly.

## Methods and Associated Functions
- Implement behavior with `impl` blocks; multiple `impl` blocks are fine.
- Associated functions (no `self`) are used for constructors or utilities:
```rust
impl User {
    fn new(email: impl Into<String>) -> Self {
        Self { id: 0, email: email.into(), active: true }
    }
    fn deactivate(&mut self) { self.active = false; }
}
```
- Implement traits in separate `impl Trait for Type` blocks to keep concerns isolated.

## Traits and Trait Objects
- Traits define required behavior and optional default methods:
```rust
trait Storage {
    fn get(&self, key: &str) -> Option<String>;
    fn set(&mut self, key: &str, value: String);
    fn len(&self) -> usize { 0 } // default
}
```
- Trait bounds: `fn save<S: Storage>(store: &mut S, k: &str, v: &str)`.
- Where clauses aid readability: `fn save<S>(s: &mut S) -> Result<()> where S: Storage + Send`.
- Dynamic dispatch with trait objects when the concrete type is chosen at runtime:
```rust
fn loggers(ls: Vec<Box<dyn Write + Send>>) { /* ... */ }
```
- Auto traits: `Send` and `Sync` mark thread-safety; `Unpin` signals movable types.

## Generics and Trait Bounds
- Use generics to stay zero-cost; monomorphization emits concrete code per type.
- `impl Trait` in argument position keeps signatures tidy: `fn fetch(client: impl HttpClient)`.
- Blanket implementations are powerful but should avoid trait conflicts.
- Newtype pattern avoids orphan rules when you need to implement foreign traits for foreign types:
```rust
struct PrettyDate(chrono::NaiveDate);
impl Display for PrettyDate { /* ... */ }
```

## Lifetimes in Practice
- The compiler usually infers lifetimes; annotate when multiple input references relate to the output:
```rust
fn longest<'a>(a: &'a str, b: &'a str) -> &'a str { if a.len() > b.len() { a } else { b } }
```
- Structs holding references must name lifetimes on the struct and impl blocks.
- Avoid unnecessary lifetimes on owned data; prefer `String` over `&str` when ownership is clearer.

## Collections and Ownership Patterns
- `Vec<T>` for dynamic arrays; `VecDeque<T>` for queue/stack; `HashMap`/`HashSet` for fast lookup; `BTreeMap`/`BTreeSet` for ordered traversal.
- Store borrowed data with care: using `HashMap<String, String>` is simpler than managing lifetimes for `&str`.
- Reference-counted owners: `Rc<T>` (single-threaded) and `Arc<T>` (thread-safe). Combine `Arc<T>` with interior mutability (e.g., `Mutex<T>`) when shared mutation is required.
- Interior mutability: `Cell<T>` (Copy types) and `RefCell<T>` (runtime borrow checking) trade compile-time guarantees for flexibility.

## Iterators and Functional Style
- Any type implementing `Iterator` can use adapters like `map`, `filter`, `flat_map`, `take`, and consumers like `collect`, `fold`.
```rust
let odds: Vec<_> = (0..10).filter(|n| n % 2 == 1).collect();
let sum: i32 = odds.iter().copied().sum();
```
- Implement custom iterators by defining `next`. Use `into_iter` to consume, `iter` to borrow, `iter_mut` to mutably borrow.
- Laziness keeps pipelines efficient; fuse with `Iterator::fuse()` to stop after `None`.

## Error Handling Patterns
- Propagate recoverable errors with `Result<T, E>` and the `?` operator; prefer small, composable error enums for libraries.
- Use crates:
  - `thiserror` for ergonomic library error definitions.
  - `anyhow` for application-level errors where context matters more than exact types.
- Enrich context: `fs::read_to_string(path).with_context(|| format!("reading {}", path.display()))?;`.
- Convert fallible initialization into builders that return `Result<Self>` to keep constructors infallible where possible.
