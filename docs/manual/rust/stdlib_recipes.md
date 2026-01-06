# Rust Standard Library Recipes

**Overview:** Practical stdlib patterns for everyday tasks. See [Rust Docs Overview](index.md) for navigation.

## Table of Contents
- Files & Paths
- Environment & Args
- Time & Scheduling
- Collections & Iterators
- Parsing & Serialization (stdlib-side)
- Error Context and Propagation

## Files & Paths
- Paths are `Path`/`PathBuf`; prefer `Path` arguments and `PathBuf` for owned storage.
```rust
use std::{fs, path::Path};
fn read_text(p: &Path) -> std::io::Result<String> {
    fs::read_to_string(p)
}
```
- Atomic-ish writes: write to temp then rename to avoid torn files:
```rust
use std::{fs, io::Write, path::Path};
fn write_atomic(path: &Path, data: &[u8]) -> std::io::Result<()> {
    let tmp = path.with_extension("tmp");
    {
        let mut f = fs::File::create(&tmp)?;
        f.write_all(data)?;
        f.sync_all()?;
    }
    fs::rename(tmp, path)
}
```
- Directory walking: `fs::read_dir` for shallow, `walkdir` crate for recursive.

## Environment & Args
- Read env vars with `std::env::var`; provide defaults via `unwrap_or_else`:
```rust
let port: u16 = std::env::var("PORT").unwrap_or_else(|_| "8080".into()).parse()?;
```
- CLI args: `std::env::args_os` handles non-UTF8; for real CLIs, prefer `clap` (see `cli_web_db.md`).

## Time & Scheduling
- `std::time::Instant` for measuring durations; `SystemTime` for timestamps.
```rust
let start = std::time::Instant::now();
do_work();
println!("took {:?}", start.elapsed());
```
- Sleep: `std::thread::sleep(Duration::from_millis(50))`; in async contexts use runtime-specific sleeps.

## Collections & Iterators
- Prefer iterator adapters to indexed loops:
```rust
let caps: Vec<_> = items.iter().map(|s| s.to_ascii_uppercase()).collect();
```
- De-dupe while preserving order with `IndexSet` (from `indexmap` crate) or stable sort + dedup if reordering is acceptable.
- Slicing: `&vec[..]`, `split_at`; beware panics on out-of-bounds.

## Parsing & Serialization (stdlib-side)
- Basic parsing via `str::parse`:
```rust
let n: i64 = "42".parse()?;
```
- Simple CSV-like splitting:
```rust
let fields: Vec<_> = line.split(',').map(str::trim).collect();
```
- For real formats (JSON, TOML, YAML), use `serde` ecosystem (see `cli_web_db.md`).

## Error Context and Propagation
- Wrap I/O with context using `anyhow::Context`:
```rust
use anyhow::Context;
fn load(path: &Path) -> anyhow::Result<String> {
    std::fs::read_to_string(path)
        .with_context(|| format!("reading {}", path.display()))
}
```
- Custom library errors: define small enums with `thiserror` to keep APIs precise.

## Quick Checklist
- Use `Path`/`PathBuf`, not raw strings, for file paths.
- Always surface context on I/O errors.
- Benchmark with `Instant`, not `SystemTime`.
- Reach for crates (`walkdir`, `rayon`) when std lacks ergonomics; keep the unsafe boundary in the crate, not your app.
