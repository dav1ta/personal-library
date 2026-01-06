# Async Patterns and Backpressure

**Overview:** Patterns for async streams, structured concurrency, cancellation, and backpressure. Linked from [Rust Docs Overview](index.md).

## Table of Contents
- Structured Concurrency
- Streams and Pipelines
- Bounded Concurrency and Backpressure
- Cancellation and Shutdown
- Timeouts and Retries
- Async Testing Patterns

## Structured Concurrency
- Prefer joining supervised tasks rather than detached fire-and-forget:
```rust
use tokio::try_join;

async fn main_tasks() -> anyhow::Result<()> {
    try_join!(sync_cache(), serve_http())?;
    Ok(())
}
```
- Use task groups (e.g., `tokio::task::JoinSet`) to manage lifetimes collectively.

## Streams and Pipelines
- Convert iterables to streams with `tokio_stream::iter` or `futures::stream::iter`.
- Map and buffer streams:
```rust
use futures::{StreamExt, TryStreamExt};

let results: Vec<_> = urls
    .into_iter()
    .map(|u| fetch(u))
    .collect::<futures::stream::FuturesUnordered<_>>()
    .try_collect()
    .await?;
```
- For order-preserving concurrency, use `buffered`; for max-throughput unordered results, use `buffer_unordered`.

## Bounded Concurrency and Backpressure
- Bound work with semaphores or bounded channels:
```rust
use tokio::sync::Semaphore;
let sem = Semaphore::new(8);
for job in jobs {
    let permit = sem.clone().acquire_owned().await?;
    tokio::spawn(async move {
        let _permit = permit;
        process(job).await;
    });
}
```
- Prefer bounded `mpsc` channels to avoid unbounded memory growth; monitor lag with `Receiver::len`.

## Cancellation and Shutdown
- Cancellation is cooperative: dropping a task’s `JoinHandle` detaches it (the task keeps running). Use `JoinHandle::abort`, a `CancellationToken`, or explicit shutdown signals.
- Propagate shutdown signals through channels or `watch`:
```rust
use tokio::{select, sync::watch};

let (tx, mut rx) = watch::channel(());
tokio::spawn(async move {
    select! {
        _ = worker() => {}
        _ = rx.changed() => {}
    }
});
// trigger shutdown
let _ = tx.send(());
```
- Use `select!` to race tasks with cancellation or timeouts.

## Timeouts and Retries
- Apply `tokio::time::timeout` around awaited work; inspect `Elapsed` separately from business errors.
- Add bounded retries with jitter using `tower::retry::Policy` or `backoff` crate.

## Async Testing Patterns
- Use `#[tokio::test(flavor = "multi_thread", worker_threads = 2)]` when tests spawn tasks.
- Avoid real sleeps; use time control (`tokio::time::pause`, `advance`) to test timers deterministically.
- For stream tests, collect limited items and assert ordering and completion behavior.

Next: [Nutrition](../other/nutr.md)
