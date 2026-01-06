# Async, Concurrency, and Parallelism

**Overview:** See the consolidated navigation in [Rust Docs Overview](index.md).

## Table of Contents
- Concurrency Model and Send/Sync
- Threads and Message Passing
- Shared State: Arc, Mutex, RwLock
- Async Foundations
- Tokio Patterns (applies to async-std with minor changes)
- Cancellation, Timeouts, and Backpressure
- Blocking and Async Interop
- Testing Concurrent Code

## Concurrency Model and Send/Sync
- Rust prevents data races at compile time. A type is
  - `Send` if it is safe to move to another thread,
  - `Sync` if `&T` is safe to share between threads.
- `Send + 'static` often bounds spawned tasks to avoid borrowed data escaping its scope.
- `Send`/`Sync` are auto traits; custom unsafe impls are rare and should be justified.

## Threads and Message Passing
- Spawn threads with `std::thread::spawn` and join via `JoinHandle::join`.
```rust
use std::thread;
let handle = thread::spawn(|| 2 + 2);
let result = handle.join().unwrap();
```
- Prefer message passing (`std::sync::mpsc` or `crossbeam::channel`) to reduce shared mutable state:
```rust
let (tx, rx) = std::sync::mpsc::channel();
for i in 0..4 {
    let tx = tx.clone();
    std::thread::spawn(move || tx.send(i * i).unwrap());
}
for val in rx.iter().take(4) {
    println!("{val}");
}
```

## Shared State: Arc, Mutex, RwLock
- Use `Arc<T>` for shared ownership across threads; combine with `Mutex<T>` for mutable access:
```rust
use std::{sync::{Arc, Mutex}, thread};
let counter = Arc::new(Mutex::new(0));
let mut handles = vec![];
for _ in 0..8 {
    let c = Arc::clone(&counter);
    handles.push(thread::spawn(move || {
        let mut n = c.lock().unwrap();
        *n += 1;
    }));
}
for h in handles { h.join().unwrap(); }
println!("count = {}", *counter.lock().unwrap());
```
- `RwLock` allows many readers or one writer; avoid if write-heavy.
- Avoid deadlocks by minimizing lock scope and keeping lock acquisition order consistent.

## Async Foundations
- `async fn` returns `impl Future`; execution requires a runtime.
- Polling is cooperative; await points yield control.
- Use `FutureExt::map`/`then` (from `futures`) for combinators, or structured concurrency with `tokio::try_join!`.

## Tokio Patterns
- Entry point: `#[tokio::main] async fn main() -> anyhow::Result<()> { ... }`
- Spawning: `tokio::spawn(async move { /* ... */ })` (tasks must be `Send + 'static`).
- Async I/O: `TcpStream`, `UdpSocket`, `File`, `AsyncRead/AsyncWrite` via `tokio::io`.
- Concurrency control:
  - `tokio::sync::mpsc` for channels.
  - `Semaphore` to cap concurrency.
  - `Mutex`/`RwLock` in `tokio::sync` are async-aware.
- Timers and cancellation:
```rust
use tokio::{time, select};
select! {
    _ = time::sleep(Duration::from_secs(1)) => println!("timeout"),
    res = do_work() => println!("done: {:?}", res),
}
```

## Cancellation, Timeouts, and Backpressure
- Cancellation is cooperative; dropping a `JoinHandle` *detaches* the task (it keeps running). To stop tasks, use `JoinHandle::abort`, a `CancellationToken`, or explicit shutdown signals (channels, `watch`, `Notify`).
- Apply timeouts with `tokio::time::timeout(duration, fut)`; handle `Elapsed`.
- For streams, apply backpressure with bounded channels or `buffer_unordered`/`buffered` to cap in-flight tasks.

## Blocking and Async Interop
- Do not perform blocking I/O or CPU-heavy work on the async runtime threads; offload with `tokio::task::spawn_blocking` or a dedicated thread pool.
- Bridging sync APIs: wrap in `spawn_blocking` or expose a sync facade for callers not on a runtime.
- Mixing runtimes is rarely necessary; prefer one runtime per process. For synchronous binaries, use `tokio::runtime::Runtime` when embedding.

## Testing Concurrent Code
- Use `#[tokio::test]` or `#[actix_rt::test]` for async tests.
- In threaded tests, coordinate with channels instead of sleeps; if timing matters, prefer `loom` for deterministic concurrency testing.
- Validate absence of deadlocks by keeping tests short-lived and enabling `RUST_BACKTRACE=1` for panics. Use `cargo test -- --nocapture` to see log output.

Next: [Memory & Tooling](memory.md)
