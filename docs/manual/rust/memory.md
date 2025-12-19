# Memory, Unsafe, Macros, and Tooling

**Overview:** See the consolidated navigation in [Rust Docs Overview](index.md).

## Table of Contents
- Memory Model and Smart Pointers
- Interior Mutability
- Pinning and Self-Referential Types
- Unsafe Code Guidelines
- Foreign Function Interface (FFI)
- Macros: Declarative and Procedural
- Tooling: fmt, clippy, tests, benches, docs
- Profiling, Logging, and Observability
- Packaging, Features, and Workspaces

## Memory Model and Smart Pointers
- Stack vs heap is explicit through ownership. Dropping a value runs its `Drop` implementation deterministically.
- Use `Box<T>` to allocate on the heap and give a stable address; great for large enums or trait objects.
- Reference counting:
  - `Rc<T>` for shared ownership in single-threaded scenarios.
  - `Arc<T>` for thread-safe shared ownership; combine with `Mutex<T>`/`RwLock<T>` when mutation is needed.
- Borrowed smart pointers:
  - `Cow<'a, T>` to defer allocation until mutation is required.
  - `Weak<T>` to break reference cycles when using `Rc`/`Arc`.

## Interior Mutability
- `Cell<T>` enables copy-in/copy-out mutation for `Copy` types.
- `RefCell<T>` enforces borrowing rules at runtime; panics on violation. Keep scopes narrow to avoid surprises.
- `OnceCell`/`Lazy` provide single-assignment patterns; prefer them over `static mut`.

## Pinning and Self-Referential Types
- `Pin<&mut T>` promises the value will not move in memory; needed for some async primitives and self-referential structs.
- Convert with `Box::pin` or `pin_utils::pin_mut!`. Avoid self-referential types when possible; refactor to store indices or handles instead of references.

## Unsafe Code Guidelines
- Unsafe unlocks five powers: dereferencing raw pointers, calling `unsafe fn`, implementing `unsafe trait`, accessing/modifying mutable statics, and inline assembly.
- Keep `unsafe` blocks small and documented with invariants you rely on.
- Prefer safe abstractions: look for crates that already encapsulate the unsafe (e.g., `bytemuck`, `bytes`, `parking_lot`).

## Foreign Function Interface (FFI)
- Match C layout with `#[repr(C)]` and plain field types:
```rust
#[repr(C)]
pub struct Point { pub x: f64, pub y: f64 }
```
- Declare externs: `extern "C" { fn c_func(arg: *const c_char) -> c_int; }`.
- Export Rust to C consumers with `#[no_mangle] pub extern "C" fn` and stable signatures.
- Use `cbindgen` to generate C headers and `bindgen` to generate Rust bindings to C libraries. Ensure safety boundaries are enforced in wrapper functions.

## Macros: Declarative and Procedural
- Declarative (`macro_rules!`) for pattern-based expansion:
```rust
macro_rules! map {
    ($($k:expr => $v:expr),* $(,)?) => {{
        let mut m = std::collections::HashMap::new();
        $( m.insert($k, $v); )*
        m
    }};
}
```
- Procedural macros run at compile time and operate on token streams:
  - Derive macros (`#[derive(Serialize, Deserialize)]`)
  - Attribute macros (`#[tokio::main]`)
  - Function-like macros (`foo!(...)`)
- Keep proc-macro crates minimal and fast; test with `cargo expand` to view expansions.

## Tooling: fmt, clippy, tests, benches, docs
- Format: `cargo fmt` (enforce in CI).
- Lint: `cargo clippy -- -D warnings` for stricter hygiene.
- Unit tests live alongside code with `#[cfg(test)] mod tests { ... }`; integration tests go in `tests/`.
- Doc tests ensure examples stay correct. Use `///` comments and run `cargo test`.
- Benchmarks: use `criterion` for stable Rust (since `cargo bench` is nightly-only by default).
- Docs: `cargo doc --open` builds API docs; add module-level docs with `//!` at the top of a file.

## Profiling, Logging, and Observability
- Logging: pair `log` with `env_logger` for minimal setup, or adopt `tracing` for structured, async-aware spans.
- Profiling: `cargo flamegraph` (with `perf`), `dhat-rs`/`heaptrack` for allocations, `tokio-console` for runtime insight into async tasks.
- Metrics: `metrics` + `prometheus` exporter or `opentelemetry` for distributed traces.

## Packaging, Features, and Workspaces
- Use Cargo features to toggle optional dependencies or functionality:  
```toml
[features]
default = ["cli"]
cli = ["clap"]
serde = ["serde/derive"]
```
- Prefer additive, non-breaking features; avoid mutually exclusive flags unless documented.
- Workspaces reduce duplication of dependencies and share lockfiles; centralize common tooling (lint, fmt) at the root.
- Versioning: follow semver; keep `authors`/`repository` metadata; publish with `cargo publish --dry-run` before releasing.
