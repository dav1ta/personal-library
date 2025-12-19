# Rust Docs Overview

## Table of Contents
- [Fundamentals](vars.md)
- [Data, Traits, Generics, and Iterators](generators.md)
- [Async, Concurrency, and Parallelism](object.md)
- [Memory, Unsafe, Macros, and Tooling](memory.md)
- [Stdlib Recipes](stdlib_recipes.md)
- [Cargo & Tooling](tooling_cargo.md)
- [Testing](testing.md)
- [CLI / Web / DB Recipes](cli_web_db.md)
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
