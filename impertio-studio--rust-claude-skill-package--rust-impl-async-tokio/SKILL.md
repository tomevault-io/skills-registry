---
name: rust-impl-async-tokio
description: > Use when this capability is needed.
metadata:
  author: Impertio-Studio
---

# rust-impl-async-tokio

The **tokio runtime implementation** skill: how to start a tokio runtime, spawn and join async tasks, race futures with `select!`, apply timeouts, cancel work safely, and use `JoinSet` for structured concurrency. Targets tokio 1.x on Rust 1.85+, edition 2024.

Cross-references: [[rust-core-async-runtime]] (Future trait, Waker model, why a runtime is needed), [[rust-syntax-async-await]] (async fn, .await, Send/Sync bounds across `.await`), [[rust-impl-channels]] (`tokio::sync::mpsc`, `oneshot`, `broadcast`, `watch`), [[rust-errors-async]] (JoinError, Elapsed, propagating panics across task boundaries).

---

## When to use this skill

- User adds `tokio` to `Cargo.toml` and asks which features (`rt`, `rt-multi-thread`, `macros`, `time`, `sync`, `full`).
- User annotates `main` with `#[tokio::main]` or hand-builds a `Runtime`.
- User calls `tokio::spawn`, gets `JoinHandle<T>`, asks how to wait for it or abort it.
- User has CPU-bound or blocking sync work inside async code and asks where to run it.
- User wants to race two futures or apply a timeout.
- User wants to spawn a group of related tasks and cancel them together (structured concurrency).
- User sees `panicked at 'there is no reactor running'` or `cannot block the current thread from within a runtime`.

For the `Future` trait, pinning, and runtime architecture see [[rust-core-async-runtime]]. For async fn / `.await` syntax and Send-across-await see [[rust-syntax-async-await]]. For channels see [[rust-impl-channels]]. For errors see [[rust-errors-async]].

---

## Decision tree: which runtime flavor

```
Single-threaded app, !Send data held across .await, lowest overhead?
   -> Runtime::new_current_thread()    or    #[tokio::main(flavor = "current_thread")]

Multi-core throughput, work-stealing across threads, default for servers?
   -> Runtime::new_multi_thread()      or    #[tokio::main]              (default)

Embedded inside another runtime / async-std / smol?
   -> NEVER build a nested tokio runtime; use Handle::current() to schedule.
```

ALWAYS pick `current_thread` when tasks hold `Rc`, `RefCell`, or any `!Send` type across `.await`. NEVER pick `multi_thread` and then sprinkle `Arc<Mutex<_>>` everywhere to silence Send errors; switch flavor instead.

---

## Decision tree: where to run blocking code

```
Is the operation < 100 microseconds and non-IO?
   YES -> inline async, no spawn needed.
   NO  -> continue.

Is it CPU-bound (parse, compress, crypto, math)?
   YES -> rayon thread pool        OR     tokio::task::spawn_blocking      (small ops only)
   NO  -> continue.

Is it synchronous IO with no async equivalent (legacy driver, FFI)?
   YES -> tokio::task::spawn_blocking      (returns JoinHandle)
          OR tokio::task::block_in_place   (multi_thread only, current task tagged)

Is an async equivalent available (tokio::fs, tokio::net, reqwest, sqlx)?
   YES -> use the async API. NEVER call std::fs or std::net inside async fn.
```

ALWAYS prefer `spawn_blocking` over `block_in_place` when you do not need to keep the task identity. NEVER call `std::thread::sleep`, `std::sync::Mutex::lock()` across `.await`, or synchronous `reqwest::blocking::*` inside async code: each one parks a worker thread and starves every other task on that worker.

---

## Runtime setup

The `#[tokio::main]` macro is sugar that desugars to a `Runtime::new()` plus `block_on(async { ... })`. Pick the explicit form when you need to configure threads, worker stack size, or runtime hooks.

### Sugared form

```rust
// Cargo.toml needs: tokio = { version = "1", features = ["macros", "rt-multi-thread"] }
#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let body = reqwest::get("https://example.com").await?.text().await?;
    println!("{}", body);
    Ok(())
}
```

ALWAYS enable the `macros` feature for `#[tokio::main]`. ALWAYS enable `rt-multi-thread` (default flavor) or `rt` (current_thread flavor); without them the macro panics at startup.

### Single-threaded variant

```rust
#[tokio::main(flavor = "current_thread")]
async fn main() {
    let local = std::rc::Rc::new(42);          // !Send, fine on current_thread
    do_work(local.clone()).await;
}
```

### Explicit form (custom thread count, hooks, worker stack)

```rust
fn main() -> std::io::Result<()> {
    let runtime = tokio::runtime::Builder::new_multi_thread()
        .worker_threads(4)
        .thread_name("workers")
        .thread_stack_size(2 * 1024 * 1024)
        .enable_all()                          // time + io drivers
        .build()?;

    runtime.block_on(async {
        // your async work
    });
    Ok(())
}
```

NEVER call `Runtime::new()` from inside an `async fn` running on tokio: it panics with `Cannot start a runtime from within a runtime`. Use `Handle::current()` to schedule onto the existing one. See `references/methods.md` for `Handle` APIs.

---

## Spawning tasks

`tokio::spawn(future)` returns a `JoinHandle<T>`. The future starts running immediately on the runtime; you do not need to `.await` the handle for the task to make progress.

```rust
let handle: tokio::task::JoinHandle<u32> = tokio::spawn(async {
    tokio::time::sleep(std::time::Duration::from_millis(50)).await;
    42
});
let value = handle.await?;     // returns Result<T, JoinError>
```

Send bounds:

- `multi_thread` runtime: the future must be `Send + 'static`. The compiler checks every type held across `.await`.
- `current_thread` runtime: the future must be `'static` but not `Send`.

`JoinError` carries one of three causes: the task panicked, the task was cancelled (`.abort()` or drop), or it was deliberately aborted with `AbortHandle`. ALWAYS match on `err.is_panic()` vs `err.is_cancelled()`; never assume `?` propagates a meaningful error message. Full method list in `references/methods.md`.

### Detaching

Dropping a `JoinHandle` does NOT abort the task on tokio. The task keeps running until it finishes or the runtime shuts down; its return value is discarded. ALWAYS keep the handle when you need the result; ALWAYS hold an `AbortHandle` if you need remote cancellation without owning the handle.

### Aborting

```rust
let handle = tokio::spawn(long_running());
handle.abort();                 // schedules cancellation, returns immediately
let result = handle.await;      // Err(JoinError) with is_cancelled() == true
```

Abort is cooperative: tokio cancels the task at the next `.await` point. Sync code between two `.await` points runs to completion. NEVER rely on `.abort()` to stop CPU-bound code that never yields; use `spawn_blocking` plus an `Arc<AtomicBool>` cancellation flag instead.

---

## Selecting between futures: `tokio::select!`

`select!` polls multiple futures concurrently and returns as soon as one completes. The other branches are cancelled (their futures are dropped without re-polling).

```rust
tokio::select! {
    msg = rx.recv() => handle(msg),
    _ = tokio::time::sleep(Duration::from_secs(5)) => timeout(),
    _ = shutdown.cancelled() => shutdown_handler(),
}
```

Branches are polled in **random order** by default to avoid starvation. ALWAYS pass `biased;` as the first token when you need deterministic priority (`biased;` makes branches poll top-to-bottom).

```rust
tokio::select! {
    biased;
    _ = shutdown.cancelled() => return,   // always check shutdown first
    msg = rx.recv() => handle(msg),
}
```

Cancellation safety: any future inside `select!` may be cancelled at any `.await`. NEVER put a future that drops important state mid-step inside `select!` unless that future is documented as cancellation-safe. `tokio::sync::mpsc::Receiver::recv` and `tokio::time::sleep` are safe; many third-party futures are not. See `references/anti-patterns.md`.

Optional pre-condition (`if guard`):

```rust
tokio::select! {
    msg = rx.recv(), if !paused => handle(msg),
    _ = wakeup.notified() => paused = false,
}
```

Optional `else` branch fires when every branch's `if`-guard is false:

```rust
tokio::select! {
    msg = rx.recv(), if !paused => handle(msg),
    else => return,
}
```

---

## Timeouts

`tokio::time::timeout(duration, future)` returns `Result<T, Elapsed>`. The inner future is dropped (cancelled) when the deadline expires.

```rust
use tokio::time::{timeout, Duration};

match timeout(Duration::from_secs(3), client.fetch(url)).await {
    Ok(Ok(body)) => process(body),
    Ok(Err(e)) => log_error(e),         // inner future returned Err
    Err(_elapsed) => log_timeout(),     // deadline hit
}
```

For a fixed wall-clock deadline use `tokio::time::timeout_at(Instant, future)`. NEVER wrap a non-cancellation-safe future in `timeout`: the inner future will be dropped mid-step and may leak resources, double-close, or split a multi-step transaction. Wrap such futures in their own task and use a `CancellationToken` + `select!` instead.

---

## Cancellation semantics

Tokio cancellation is **drop-based**: when a future is dropped, every `.await` chain above it sees no more progress. There is no panic, no exception. Destructors run as normal.

Three primary cancellation triggers:

1. **`JoinHandle::abort()`** schedules the task for cancellation at the next `.await`.
2. **`select!`** drops the losing branches.
3. **`timeout`** drops the inner future on deadline.

To make your code cancellation-tolerant: keep `.await` points cheap, use `tokio_util::sync::CancellationToken` for cooperative shutdown signals, and use `Drop` impls or `scopeguard::defer!` for cleanup that must run even when cancelled.

```rust
let token = tokio_util::sync::CancellationToken::new();
let child = token.child_token();

tokio::spawn(async move {
    tokio::select! {
        _ = child.cancelled() => cleanup(),
        _ = work_loop() => {}
    }
});

// elsewhere:
token.cancel();                         // all child tokens fire
```

ALWAYS prefer `CancellationToken` over `AtomicBool` for async cancellation: `cancelled()` is awaitable and integrates with `select!`. NEVER busy-loop on an atomic flag inside async code.

---

## Structured concurrency with `JoinSet`

`JoinSet<T>` owns a group of spawned tasks, gives unordered `join_next()` access to whichever finishes first, and aborts every task on drop. This is the structured-concurrency primitive in tokio (NDST 12).

```rust
use tokio::task::JoinSet;

let mut set: JoinSet<u32> = JoinSet::new();
for n in 0..10 {
    set.spawn(async move { do_work(n).await });
}

while let Some(res) = set.join_next().await {
    match res {
        Ok(value) => collect(value),
        Err(e) if e.is_panic() => std::panic::resume_unwind(e.into_panic()),
        Err(e) => log_cancellation(e),
    }
}
```

Key methods:

- `spawn(future)` (multi_thread) / `spawn_local(future)` (LocalSet).
- `join_next() -> Option<Result<T, JoinError>>` returns the next finished task or `None` when empty.
- `join_next_with_id()` also returns the task `Id`.
- `abort_all()` aborts every member; pending tasks become `is_cancelled()` errors.
- `shutdown().await` aborts every task and waits for them to finish.
- `detach_all()` removes every task without aborting (the tasks keep running).

ALWAYS use `JoinSet::shutdown().await` in cleanup paths so cancelled tasks have a chance to run their destructors before the set is dropped. ALWAYS propagate panics with `resume_unwind` if you want a panicking child to terminate the parent. NEVER swallow `JoinError::Panic` silently; structured concurrency means children's failures are visible.

For a worked example (parallel HTTP fan-out with bounded concurrency, panic propagation, graceful shutdown) see `references/examples.md`.

---

## Task-local storage: `task_local!`

```rust
tokio::task_local! {
    static REQUEST_ID: String;
}

REQUEST_ID.scope("req-123".to_string(), async {
    REQUEST_ID.with(|id| println!("{}", id));
}).await;
```

Each task sees its own value. Inherited automatically by child tasks **only when spawned from inside the scope**. ALWAYS use `task_local!` for tracing contexts, request IDs, or per-request configuration. NEVER use `thread_local!` for per-task data: tokio worker threads run many tasks and the value bleeds between them.

---

## Quick checklist

- `#[tokio::main]` with `macros` + `rt-multi-thread` features in `Cargo.toml`.
- `tokio::spawn` for async work, `spawn_blocking` for sync work, `block_in_place` only on multi_thread when the task identity must stay.
- Use `JoinSet` when spawning more than two related tasks. ALWAYS join or abort on shutdown.
- Wrap external futures in `timeout` only when they are cancellation-safe.
- `select!` with `biased;` for priority; default polling order is random.
- Hold `std::sync::Mutex` only across non-`.await` regions; use `tokio::sync::Mutex` otherwise.
- Cancellation is drop; place cleanup in `Drop` impls or `scopeguard::defer!`.

---

## Reference index

- `references/methods.md` : `Runtime` / `Handle` / `JoinHandle` / `AbortHandle` / `JoinSet` API surface with signatures.
- `references/examples.md` : worked patterns (graceful shutdown, fan-out with bounded concurrency, timeout-with-retry, cancellation tokens, task-local tracing).
- `references/anti-patterns.md` : ten field-tested anti-patterns with root cause and the correct fix.

## External sources

- [Tokio API docs](https://docs.rs/tokio/latest/tokio/)
- [JoinHandle](https://docs.rs/tokio/latest/tokio/task/struct.JoinHandle.html)
- [JoinSet](https://docs.rs/tokio/latest/tokio/task/struct.JoinSet.html)
- [select! macro](https://docs.rs/tokio/latest/tokio/macro.select.html)
- [tokio::sync](https://docs.rs/tokio/latest/tokio/sync/index.html)
- [Tokio Tutorial](https://tokio.rs/tokio/tutorial)

---
> Source: [Impertio-Studio/Rust-Claude-Skill-Package](https://github.com/Impertio-Studio/Rust-Claude-Skill-Package) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
