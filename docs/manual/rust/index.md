# Rust Docs Overview

## How to Use This Section

- Learning path: `vars.md` -> `generators.md` -> `memory.md` -> `object.md` -> `async_patterns.md` -> `testing.md` / `tooling_cargo.md`.
- Review loop: revisit `memory.md` and `async_patterns.md` regularly; they carry many concepts that become clearer with practice.
- Problem-solving mode: use `stdlib_recipes.md` and `cli_web_db.md` as applied references after the fundamentals.

## Table of Contents
- [Fundamentals](vars.md)
- [Data, Traits, Generics, and Iterators](generators.md)
- [Async, Concurrency, and Parallelism](object.md)
- [Memory, Unsafe, Macros, and Tooling](memory.md)
- [Stdlib Recipes](stdlib_recipes.md)
- [Cargo & Tooling](tooling_cargo.md)
- [Testing](testing.md)
- [CLI / Web / DB Recipes](cli_web_db.md)
- [Libraries](libraries.md)
- [Async Patterns](async_patterns.md)

## Quick Links
- Install toolchain: `rustup component add rustfmt clippy rust-analyzer`
- Build & test: `cargo build`, `cargo test -- --nocapture`, `cargo clippy -- -D warnings`
- Format: `cargo fmt`
- Docs: `cargo doc --open`

## Version Notes
- Examples target stable Rust as of 2025; prefer the latest stable toolchain via `rustup update`.
- Async examples use `tokio` 1.x; adjust imports if using another runtime.
- Macros and FFI sections assume the 2021 edition unless stated otherwise.

Next: [Fundamentals](vars.md)
