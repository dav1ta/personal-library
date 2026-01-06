# Rust Libraries (Common Picks)

Concise list of commonly used crates by category. Prefer the standard library first.

## Async Runtime
- `tokio` (default)
- `async-std` (alt)

## Web + HTTP
- `axum` or `actix-web` (framework)
- `hyper` + `tower` (lower-level)
- `reqwest` (client)

## Serialization
- `serde`
- `serde_json`, `toml`

## CLI + Config
- `clap`
- `config` or `envy`

## Error Handling
- `anyhow` (apps)
- `thiserror` (libs)

## Logging / Tracing
- `tracing` + `tracing-subscriber`

## Data Access
- `sqlx` (async, compile-time checked)
- `diesel` (sync / typed DSL)
- `tokio-postgres`, `rusqlite` (drivers)

## Testing
- `proptest`
- `insta`

## Concurrency / Parallel
- `rayon`
- `crossbeam`

## Time / IDs
- `time` or `chrono`
- `uuid`

## TLS
- `rustls`

## Notes
- Pick one crate per category to keep the dependency graph simple.
- Check MSRV and feature flags before adopting a crate.

Next: [Async Patterns](async_patterns.md)
