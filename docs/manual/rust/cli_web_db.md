# CLI, Web, and Data Access Recipes

**Overview:** Quick-start patterns with popular crates. Linked from [Rust Docs Overview](index.md).

## Table of Contents
- Command-Line Interfaces (clap)
- Configuration and Serialization (serde)
- HTTP Clients (reqwest)
- HTTP Servers (axum + hyper)
- Databases (sqlx example)
- Logging and Tracing

## Command-Line Interfaces (clap)
- Derive-based CLI:
```rust
use clap::{Parser, Subcommand};

#[derive(Parser)]
#[command(version, about)]
struct Cli {
    #[command(subcommand)]
    cmd: Command,
}

#[derive(Subcommand)]
enum Command {
    Serve { #[arg(long, default_value_t = 8080)] port: u16 },
    Greet { name: String },
}

fn main() {
    let cli = Cli::parse();
    println!("{:?}", cli.cmd);
}
```
- Add shell completions with `clap_complete` if desired.

## Configuration and Serialization (serde)
- Define config structs with defaults:
```rust
use serde::Deserialize;

#[derive(Deserialize)]
struct Config {
    #[serde(default = "default_port")]
    port: u16,
    #[serde(default)]
    features: Vec<String>,
}
fn default_port() -> u16 { 8080 }
```
- Load from JSON/TOML/YAML using `serde_json`, `toml`, or `serde_yaml`:
```rust
let cfg: Config = toml::from_str(&std::fs::read_to_string("Config.toml")?)?;
```

## HTTP Clients (reqwest)
- Async GET with JSON decode:
```rust
use reqwest::Client;
use serde::Deserialize;

#[derive(Deserialize)]
struct Todo { id: u32, title: String }

async fn fetch() -> anyhow::Result<Vec<Todo>> {
    let client = Client::new();
    let resp = client
        .get("https://jsonplaceholder.typicode.com/todos")
        .send()
        .await?
        .error_for_status()?
        .json()
        .await?;
    Ok(resp)
}
```
- Timeouts and retries: set `ClientBuilder::timeout`, combine with `tokio::time::timeout` or `tower::retry`.

## HTTP Servers (axum + hyper)
- Minimal axum app:
```rust
use axum::{routing::get, Router};

async fn root() -> &'static str { "ok" }

#[tokio::main]
async fn main() -> anyhow::Result<()> {
    let app = Router::new().route("/", get(root));
    axum::Server::bind(&"0.0.0.0:8080".parse()?)
        .serve(app.into_make_service())
        .await?;
    Ok(())
}
```
- Middlewares: add `tower::ServiceBuilder` layers for tracing, timeouts, compression.

## Databases (sqlx example)
- Async Postgres pool and query:
```rust
use sqlx::{PgPool, Row};

#[tokio::main]
async fn main() -> anyhow::Result<()> {
    let pool = PgPool::connect("postgres://postgres:postgres@localhost/postgres").await?;
    let row = sqlx::query("select 1 + $1 as sum").bind(2i32).fetch_one(&pool).await?;
    let sum: i32 = row.get("sum");
    println!("{sum}");
    Ok(())
}
```
- Enable offline mode for CI: `cargo sqlx prepare -- --lib` and include `sqlx-data.json`.
- For compile-time checked queries, use `query!`/`query_as!` with `sqlx` feature `macros`.

## Logging and Tracing
- Structured, async-friendly tracing:
```rust
use tracing::{info, instrument};
use tracing_subscriber::FmtSubscriber;

#[tokio::main]
async fn main() {
    tracing_subscriber::fmt::init();
    serve().await;
}

#[instrument]
async fn serve() {
    info!("starting");
}
```
- Export to observability backends with `tracing-opentelemetry` if needed.

Next: [Libraries](libraries.md)
