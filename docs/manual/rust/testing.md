# Testing in Rust

**Overview:** Unit, integration, doc, async, property, and snapshot testing patterns. Linked from [Rust Docs Overview](index.md).

## Table of Contents
- Unit Tests (inline)
- Integration Tests
- Doc Tests
- Async Tests
- Property Testing
- Snapshot Testing
- Fakes, Mocks, and Test Doubles
- Deterministic Concurrency
- Test Hygiene Checklist

## Unit Tests (inline)
- Place alongside code with `#[cfg(test)]`:
```rust
#[cfg(test)]
mod tests {
    use super::*;
    #[test]
    fn adds() { assert_eq!(add(2, 3), 5); }
}
```
- Keep fixtures small; extract helpers inside the test module to avoid exporting them.

## Integration Tests
- Put files in `tests/` (one crate per file). Tests import the public API as an external user:
```rust
// tests/smoke.rs
use mylib::Server;
#[test]
fn starts() {
    let s = Server::new("localhost:0").unwrap();
    assert!(s.addr().port() > 0);
}
```
- Common setup can live in `tests/common/mod.rs` and be imported with `mod common;`.

## Doc Tests
- Examples in `///` are compiled and run by default:
```rust
/// ```rust
/// assert_eq!(mycrate::double(2), 4);
/// ```
```
- For long examples, hide boilerplate with `#` prefix. Disable execution with `ignore` when necessary but prefer runnable examples.

## Async Tests
- Use runtime-specific attributes:
```rust
#[tokio::test]
async fn fetches() { /* ... */ }
```
- For `async-std`: `#[async_std::test]`.
- Avoid blocking calls; use runtime timers and channels.

## Property Testing
- Use `proptest` or `quickcheck` for invariants:
```rust
use proptest::prelude::*;
proptest! {
    #[test]
    fn rev_rev_is_id(xs: Vec<u8>) {
        let rev: Vec<_> = xs.clone().into_iter().rev().collect();
        let back: Vec<_> = rev.into_iter().rev().collect();
        prop_assert_eq!(xs, back);
    }
}
```
- Shrinking finds minimal counterexamples; keep strategies focused to avoid huge cases.

## Snapshot Testing
- `insta` captures structured outputs:
```rust
use insta::assert_snapshot;
#[test]
fn render_card() {
    let html = render();
    assert_snapshot!(html);
}
```
- Commit snapshots; when behavior changes intentionally, update via `cargo insta review`.

## Fakes, Mocks, and Test Doubles
- Prefer real logic with in-memory fakes (e.g., `HashMap`-backed repo) over mocks.
- If mocking is needed, use trait injection and small surfaces. Crates: `mockall`, `double`.

## Deterministic Concurrency
- Use channels and explicit signals instead of sleeps.
- `loom` explores interleavings to expose data races in concurrent structures.

## Test Hygiene Checklist
- Run `cargo test`, `cargo clippy --tests`, and `cargo fmt` in CI.
- Keep tests hermetic: avoid network/filesystem unless explicitly scoped; use tempdirs with `tempfile`.
- Name tests for behavior, not implementation details.

Next: [CLI / Web / DB](cli_web_db.md)
