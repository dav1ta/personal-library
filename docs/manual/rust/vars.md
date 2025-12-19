# Rust Fundamentals

**Overview:** See the consolidated navigation in [Rust Docs Overview](index.md).

## Table of Contents
- Getting Set Up
- Project Layout
- Values, Mutability, and Shadowing
- Core Types
- Ownership and Borrowing
- Control Flow and Pattern Matching
- Functions, Closures, and Methods
- Modules, Visibility, and Crates
- Idioms and Pitfalls

## Getting Set Up
- Install with `rustup` and pick a toolchain: `rustup toolchain install stable` or `rustup default stable`.
- Add components you’ll use daily: `rustup component add clippy rustfmt rust-analyzer`.
- Create a new project: `cargo new hello-rust` (binary) or `cargo new --lib mylib`.

## Project Layout
- `src/main.rs` or `src/lib.rs` are entry points; integration tests live in `tests/`, examples in `examples/`, benches in `benches/`.
- Prefer workspaces for multi-crate repos: `Cargo.toml` with `[workspace]` plus member crates in subdirs.
- Profiles: use `cargo build --release` for optimized binaries; control via `[profile.release]` in `Cargo.toml`.

## Values, Mutability, and Shadowing
- Bindings are immutable by default: `let x = 1;`.
- Opt into mutation: `let mut counter = 0; counter += 1;`.
- Shadowing creates a new binding with the same name; useful for type changes or refinements:
```rust
let spaces = "   ";
let spaces: usize = spaces.len();
```
- Constants use `const NAME: Type = value;` and are compile-time, globally usable.

## Core Types
- Scalars: `i32`, `u64`, `f64`, `bool`, `char` (Unicode scalar), and sized pointers like `usize`.
- Strings: `String` (owned, growable) vs `&str` (borrowed slice). Convert with `let s = "hi".to_string();`.
- Collections: `Vec<T>`, `VecDeque<T>`, `HashMap<K, V>`, `BTreeMap<K, V>`, `HashSet<T>`, `BTreeSet<T>`. Choose BTree* when deterministic ordering matters.
- Tuples and arrays: tuples can mix types; arrays have fixed length and element type.
- Option and Result:
```rust
let maybe: Option<i32> = Some(5);
let parsed: Result<u32, std::num::ParseIntError> = "42".parse();
```

## Ownership and Borrowing
- Each value has a single owner; moving transfers ownership:
```rust
let a = String::from("hi");
let b = a;            // a is moved; cannot be used
```
- Types that implement `Copy` (numbers, bool, chars, `&T`) are duplicated on assignment.
- Borrowing allows temporary access without transfer:
```rust
fn len(s: &String) -> usize { s.len() }
fn push(s: &mut String) { s.push('!'); }
```
- Borrow rules: at any time either many immutable borrows **or** one mutable borrow; all borrows must outlive their uses.
- Slices (`&[T]`, `&str`) are borrowed views into contiguous data.
- RAII and `Drop`: resources cleaned automatically when the owner goes out of scope.

## Control Flow and Pattern Matching
- Expressions everywhere: `let y = if cond { 1 } else { 0 };`
- `loop`, `while`, `for` with iterators:
```rust
for (i, v) in vec.iter().enumerate() {
    println!("{i}: {v}");
}
```
- Pattern matching is exhaustive and refines types:
```rust
match parsed {
    Ok(n) if n % 2 == 0 => println!("even {n}"),
    Ok(n) => println!("odd {n}"),
    Err(e) => eprintln!("parse error: {e}"),
}
```
- `if let` / `while let` handle common Option/Result cases concisely.

## Functions, Closures, and Methods
- Functions declare parameter and return types explicitly: `fn add(a: i32, b: i32) -> i32 { a + b }`.
- Closures capture environment by reference, mutable reference, or move:
```rust
let mut n = 0;
let mut inc = || { n += 1; n };
```
- Methods live in `impl` blocks; use `Self` to reference the type:
```rust
struct Point { x: f64, y: f64 }
impl Point {
    fn origin() -> Self { Self { x: 0.0, y: 0.0 } }
    fn norm(&self) -> f64 { (self.x.powi(2) + self.y.powi(2)).sqrt() }
}
```

## Modules, Visibility, and Crates
- Split files with `mod foo;` in `lib.rs`/`main.rs` and a matching `foo.rs` or `foo/mod.rs`.
- Visibility is private by default; export with `pub`, restrict with `pub(crate)` or `pub(super)`.
- Bring names into scope with `use`; re-export with `pub use` to create a curated public API.
- Organize external dependencies via `[dependencies]` in `Cargo.toml`; prefer semver-compatible caret versions like `serde = "1"` unless pinning.

## Idioms and Pitfalls
- Prefer `?` for error propagation and `Result<T, E>` for recoverable errors.
- Avoid cloning by default; reach for `.clone()` only when ownership truly must be duplicated.
- Favor iterators over indexed loops; use `into_iter` to consume, `iter` to borrow, `iter_mut` to mutably borrow.
- In async code, avoid blocking calls (e.g., `std::thread::sleep`) inside async functions; use runtime-aware versions (e.g., `tokio::time::sleep`).
