# Cargo and Project Tooling

**Overview:** Cargo workflows, profiles, features, publishing, and useful subcommands. Linked from [Rust Docs Overview](index.md).

## Table of Contents
- Workspace Layout
- Features and Dependency Management
- Profiles and Builds
- Useful Cargo Commands
- Linting and Formatting
- Publishing and Versioning
- Supply Chain & Hygiene Tools
- Cross-Compilation Tips

## Workspace Layout
- Use a root `Cargo.toml` with `[workspace]` and member crates in subdirectories:
```toml
[workspace]
members = ["crates/api", "crates/lib"]
resolver = "2"
```
- Keep shared metadata (edition, license) in each crate; avoid `[patch]` except for temporary forks.

## Features and Dependency Management
- Prefer additive features; avoid mutually exclusive toggles.
```toml
[features]
default = ["cli"]
cli = ["clap"]
serde = ["serde/derive"]
```
- Optional deps: `serde = { version = "1", optional = true }`.
- Dev-only deps go under `[dev-dependencies]` (tests/examples only).
- Lockfile: commit `Cargo.lock` for binaries; for libraries, committing is optional but often helpful for CI reproducibility.

## Profiles and Builds
- Release builds: `cargo build --release`; tune in `[profile.release]`:
```toml
[profile.release]
lto = "thin"
codegen-units = 1
panic = "abort"
```
- Dev profile can speed feedback:
```toml
[profile.dev]
opt-level = 1
debug = true
```
- Bench/profile on release builds to avoid misleading results.

## Useful Cargo Commands
- `cargo check` fast typecheck; `cargo clippy -- -D warnings` for lint gate.
- `cargo fmt` to format; `cargo test -- --nocapture` for verbose tests.
- `cargo doc --open` generate API docs.
- `cargo tree` inspect dependencies; `cargo tree -i crate` to see reverse deps.
- `cargo expand` (via `cargo install cargo-expand`) to view macro expansion.

## Linting and Formatting
- Enforce `cargo fmt` and `cargo clippy -- -D warnings` in CI.
- For MSRV, pin a toolchain (e.g., `1.77`) in CI and document it.

## Publishing and Versioning
- Dry-run before publishing: `cargo publish --dry-run`.
- Semver: bump MINOR for new features (backward compatible), PATCH for fixes, MAJOR for breaking changes.
- Fill in crate metadata: `license`, `repository`, `documentation`, `readme`.
- Tag releases in VCS to match crates.io versions.

## Supply Chain & Hygiene Tools
- `cargo-deny` checks licenses, bans, advisories.
- `cargo-outdated` spots update opportunities.
- `cargo-udeps` detects unused dependencies.
- `cargo-audit` checks vulnerabilities (uses RustSec).

## Cross-Compilation Tips
- Add targets: `rustup target add x86_64-unknown-linux-musl`.
- Use `cross` for simplified cross builds when system toolchains are messy.
- For macOS→Linux with MUSL: set `RUSTFLAGS="-C target-feature=-crt-static"` if you need dynamic linking.

Next: [Testing](testing.md)
