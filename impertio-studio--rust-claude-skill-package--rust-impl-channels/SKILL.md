---
name: rust-impl-channels
description: > Use when this capability is needed.
metadata:
  author: Impertio-Studio
---

# rust-impl-channels

A channel is a typed queue with a sender half and a receiver half: producers push values into the sender, consumers pull them from the receiver, and the queue handles synchronization. The right channel depends on three axes: *sync vs async*, *how many producers and consumers*, and *how many values flow through it over time*. Pick the wrong one and the program either blocks the executor, runs out of memory, or silently drops messages.

Cross-references: [[rust-impl-async-tokio]] (the runtime that hosts async channels and task spawning), [[rust-impl-concurrency]] (Arc / Mutex / atomics for in-place shared state without a channel), [[rust-core-stdlib-overview]] (`std::sync::mpsc` lives in the stdlib `sync` module), [[rust-errors-async]] (`.await` discipline, what a `SendError` means in an async context).

---

## When to use this skill

- User picks between `std::sync::mpsc` and `tokio::sync::mpsc` in async code.
- User asks "why does my Tokio task hang" when blocking on a `std::sync::mpsc::Receiver::recv()`.
- User has one task that needs to send a single value to another task and is reaching for `mpsc`.
- User wants to fan one event out to many subscribers.
- User wants to share the "latest configuration" or "latest state" with many watchers, not a stream.
- User asks "bounded or unbounded channel".
- User asks "what is backpressure" or "how do I apply backpressure".
- User asks "what does `SendError` mean" or "the receiver is dropped".
- User asks "do I need crossbeam-channel or flume instead of stdlib / tokio".

---

## Decision tree: which channel

```
Need to pass values from producer(s) to consumer(s)?
|
+-- Single value, one-shot, producer -> consumer?
|     -> tokio::sync::oneshot::channel()   (async)
|     -> std::sync::mpsc::sync_channel(0)  (sync, last-resort: NOT idiomatic for one-shot)
|
+-- Stream of values, ONE consumer?
|     |
|     +-- Sync (threads only, no .await on receiver)?
|     |     -> std::sync::mpsc::channel()       (unbounded)
|     |     -> std::sync::mpsc::sync_channel(n) (bounded, n>=0)
|     |     -> crossbeam_channel::bounded(n) / unbounded()   (lock-free, faster, has select!)
|     |
|     +-- Async (consumer awaits)?
|           -> tokio::sync::mpsc::channel(buffer)   (bounded, applies backpressure)
|           -> tokio::sync::mpsc::unbounded_channel() (only when producer rate is bounded elsewhere)
|           -> flume::bounded(n) / unbounded()      (works in both sync and async)
|
+-- Stream of values, MANY consumers?
|     |
|     +-- Every consumer must see EVERY message?
|     |     -> tokio::sync::broadcast::channel(capacity)
|     |        (lagging consumers receive RecvError::Lagged and must catch up)
|     |
|     +-- Every consumer wants only the LATEST value, older values may be skipped?
|           -> tokio::sync::watch::channel(initial)
|
+-- Many producers, single consumer is implicit in mpsc.
+-- Many producers, many consumers, sync, lock-free: crossbeam_channel (mpmc when both ends cloned).
```

ALWAYS pick `oneshot` for a one-shot reply (request / response pattern). NEVER use `mpsc::channel(1)` as a substitute for `oneshot`: an mpsc receiver can drain repeatedly and the type does not encode "exactly one value".

ALWAYS pick `broadcast` when every subscriber must see every message and missed messages are acceptable failure. ALWAYS pick `watch` when subscribers care only about the most recent value.

---

## std::sync::mpsc: synchronous, blocking

`std::sync::mpsc` ([std-mpsc]) is the stdlib multi-producer, single-consumer channel. It blocks the calling **thread**, so it MUST NOT be used inside an `async fn` on a Tokio task: a blocking `recv()` parks the thread and stalls every other task on that worker.

```rust
use std::sync::mpsc;
use std::thread;

fn main() {
    // Unbounded.
    let (tx, rx) = mpsc::channel::<u32>();

    // Multiple producers via clone.
    for i in 0..4 {
        let tx = tx.clone();
        thread::spawn(move || tx.send(i).unwrap());
    }
    drop(tx); // close the original; recv loop exits when ALL senders are dropped.

    while let Ok(v) = rx.recv() {
        println!("{v}");
    }
}
```

Bounded variant via `sync_channel(n)`: capacity `n` slots, `send()` blocks the thread when full.

```rust
let (tx, rx) = mpsc::sync_channel::<u32>(4); // bounded, 4 slots
tx.send(1).unwrap();    // blocks the THREAD if full
tx.try_send(2).ok();    // non-blocking, returns Err(TrySendError::Full(v)) if full
let v = rx.recv().unwrap();
```

| API | Returns | Blocking behaviour |
|-----|---------|--------------------|
| `Sender::send(v)` | `Result<(), SendError<T>>` | Unbounded: never blocks. Bounded: blocks until space. `Err` only on receiver dropped. |
| `SyncSender::try_send(v)` | `Result<(), TrySendError<T>>` | Never blocks. `Full(v)` or `Disconnected(v)`. |
| `Receiver::recv()` | `Result<T, RecvError>` | Blocks the **thread** until a value or all senders dropped. |
| `Receiver::try_recv()` | `Result<T, TryRecvError>` | Never blocks. `Empty` or `Disconnected`. |
| `Receiver::recv_timeout(d)` | `Result<T, RecvTimeoutError>` | Blocks the **thread** up to `d`. |
| `Receiver::iter()` | `Iterator<Item = T>` | Blocking iterator, ends when all senders dropped. |

ALWAYS drop the original `Sender` after cloning copies to producer threads, otherwise the loop on the receiver side never sees `RecvError`. NEVER call `rx.recv()` inside an `async fn`. Use `tokio::sync::mpsc` instead, or wrap the blocking call in `tokio::task::spawn_blocking`.

---

## tokio::sync::mpsc: async, awaitable

`tokio::sync::mpsc` ([tokio-mpsc]) provides bounded and unbounded variants whose `send` and `recv` are `async`. `send().await` parks the **task**, not the thread, so other tasks keep running.

```rust
use tokio::sync::mpsc;

#[tokio::main]
async fn main() {
    let (tx, mut rx) = mpsc::channel::<u32>(32); // bounded, capacity 32

    // Multiple producers via clone.
    for i in 0..4 {
        let tx = tx.clone();
        tokio::spawn(async move {
            tx.send(i).await.unwrap();
        });
    }
    drop(tx);

    while let Some(v) = rx.recv().await {
        println!("{v}");
    }
}
```

| API | Returns | Behaviour |
|-----|---------|-----------|
| `Sender::send(v)` | `async, Result<(), SendError<T>>` | Awaits capacity (bounded). `Err` only on receiver dropped. |
| `Sender::try_send(v)` | `Result<(), TrySendError<T>>` | Non-blocking. `Full(v)` or `Closed(v)`. |
| `Sender::send_timeout(v, d)` | `async, Result<(), SendTimeoutError<T>>` | With timeout. |
| `Sender::reserve()` | `async, Result<Permit<'_, T>, SendError<()>>` | Reserves a slot; consume with `permit.send(v)` (no extra `await`). |
| `Receiver::recv()` | `async, Option<T>` | `None` only when all senders are dropped. |
| `Receiver::try_recv()` | `Result<T, TryRecvError>` | Non-blocking. `Empty` or `Disconnected`. |
| `Receiver::close()` | `()` | Sender-side `send` will start returning `SendError`. |

Bounded is the default choice: it applies **backpressure**. If consumers fall behind, producers slow down at the `send().await` point.

```rust
let (tx, mut rx) = mpsc::channel::<Vec<u8>>(16);

// Producer slows down naturally when the queue is full.
for chunk in input_stream {
    tx.send(chunk).await?; // waits when 16 chunks are queued
}
```

`unbounded_channel()` exists ([tokio-mpsc-unbounded]) but is dangerous: a faster producer + slower consumer = unbounded memory growth.

ALWAYS use bounded `mpsc::channel(n)` unless producer throughput is provably bounded upstream (e.g. one item per HTTP request). NEVER use `unbounded_channel` to "fix" a backpressure-induced slowdown: removing backpressure trades a CPU/latency problem for an OOM crash.

ALWAYS prefer `Sender::reserve()` when you need to do work *before* sending (build the message, acquire other resources): it reserves the slot atomically and avoids holding partial state during an `.await`.

---

## tokio::sync::oneshot: one-shot single value

`tokio::sync::oneshot` ([tokio-oneshot]) is the request / response primitive: a `Sender` may send **exactly one** value, the `Receiver` is itself a `Future` that resolves with that value or with `RecvError` if the sender is dropped.

```rust
use tokio::sync::oneshot;

#[tokio::main]
async fn main() {
    let (resp_tx, resp_rx) = oneshot::channel::<u32>();

    tokio::spawn(async move {
        // Some work...
        let _ = resp_tx.send(42); // by value, consumes the sender
    });

    match resp_rx.await {
        Ok(v)  => println!("got {v}"),
        Err(_) => eprintln!("sender dropped without sending"),
    }
}
```

| API | Returns | Behaviour |
|-----|---------|-----------|
| `Sender::send(v)` | `Result<(), T>` | Consumes the sender. `Err(v)` only when receiver was dropped (gives the value back). |
| `Receiver::await` | `Result<T, RecvError>` | Resolves when sender calls `send` or is dropped. |
| `Receiver::try_recv()` | `Result<T, TryRecvError>` | Non-await variant. |
| `Receiver::close()` | `()` | Signals the sender; `Sender::send` then returns `Err`. |
| `Sender::is_closed()` | `bool` | Receiver dropped, sending is a no-op. |
| `Sender::closed()` | `async` | Resolves when receiver is dropped. |

The canonical actor-style pattern is "mpsc + oneshot": commands flow through an `mpsc::channel`, each command carries a `oneshot::Sender` for its reply.

```rust
struct Request {
    payload: String,
    resp:    oneshot::Sender<u32>,
}

let (req_tx, mut req_rx) = mpsc::channel::<Request>(32);

tokio::spawn(async move {
    while let Some(req) = req_rx.recv().await {
        let answer = req.payload.len() as u32;
        let _ = req.resp.send(answer); // ignore error: caller may have given up
    }
});

let (resp_tx, resp_rx) = oneshot::channel();
req_tx.send(Request { payload: "hello".into(), resp: resp_tx }).await?;
let len = resp_rx.await?;
```

ALWAYS use `oneshot` for request / response. NEVER use `mpsc::channel(1)`: an `mpsc::Receiver` does not encode "exactly one", and a duplicate `send` would queue or block instead of erroring.

ALWAYS ignore the `Err` from `Sender::send` if losing the reply is acceptable (caller may have already timed out). NEVER `.unwrap()` it: receiver drop is a normal life-cycle event.

---

## tokio::sync::broadcast: fan-out, every subscriber sees every message

`tokio::sync::broadcast` ([tokio-broadcast]) is multi-producer, multi-consumer; **every** receiver gets **every** value the sender publishes, until the receiver lags too far behind. Capacity is the ring-buffer size: when a receiver lags more than `capacity` messages, its next `recv()` returns `RecvError::Lagged(skipped)` and then resumes at the oldest still-available message.

```rust
use tokio::sync::broadcast;

#[tokio::main]
async fn main() {
    let (tx, mut rx1) = broadcast::channel::<u32>(16);
    let mut rx2 = tx.subscribe();

    tokio::spawn(async move {
        loop {
            match rx2.recv().await {
                Ok(v) => println!("rx2 got {v}"),
                Err(broadcast::error::RecvError::Lagged(n)) => {
                    eprintln!("rx2 lagged, missed {n}");
                    // continue, recv() will return the next available message
                }
                Err(broadcast::error::RecvError::Closed) => break,
            }
        }
    });

    for i in 0..1000 { let _ = tx.send(i); }
}
```

| API | Returns | Behaviour |
|-----|---------|-----------|
| `Sender::send(v)` | `Result<usize, SendError<T>>` | Synchronous. Returns subscriber count. `Err` only when **all** receivers are dropped. |
| `Sender::subscribe()` | `Receiver<T>` | New receiver, starts at the most recent message. |
| `Receiver::recv()` | `async, Result<T, RecvError>` | `Lagged(n)` or `Closed`. |
| `Receiver::resubscribe()` | `Receiver<T>` | Skip backlog, start fresh. |

ALWAYS handle `RecvError::Lagged` explicitly: it is a *recoverable* signal that the consumer fell behind and lost messages, not a fatal error. NEVER `.unwrap()` `broadcast::recv()` results.

ALWAYS choose a capacity matching the lag tolerance: a higher value preserves more history at the cost of memory; a low value forces faster consumers.

`Sender::send` is **synchronous** (no `.await`), unlike `mpsc::Sender::send`. The sender never blocks: when the ring is full the oldest message is dropped and lagging receivers get `Lagged`. This is the trade for "every consumer eventually catches up or sees a gap".

---

## tokio::sync::watch: latest-value, overwriting

`tokio::sync::watch` ([tokio-watch]) is multi-producer, multi-consumer **with only one value at a time**. `send` *replaces* the stored value. Consumers `borrow()` the current value or `await changed()` to be woken on the next update. Intermediate updates are coalesced: a fast producer + slow consumer sees only the latest value.

```rust
use tokio::sync::watch;

#[tokio::main]
async fn main() {
    let (tx, mut rx) = watch::channel("starting".to_string());

    tokio::spawn(async move {
        loop {
            if rx.changed().await.is_err() { break; } // sender dropped
            let v = rx.borrow().clone();
            println!("config now: {v}");
        }
    });

    tx.send("running".into()).unwrap();
    tx.send("stopping".into()).unwrap();
}
```

| API | Returns | Behaviour |
|-----|---------|-----------|
| `Sender::send(v)` | `Result<(), SendError<T>>` | Replaces stored value; `Err` if all receivers dropped. |
| `Sender::send_modify(F)` | `()` | Mutate in place; wakes receivers. |
| `Sender::subscribe()` | `Receiver<T>` | New receiver, sees current value as "unread". |
| `Receiver::changed()` | `async, Result<(), RecvError>` | Resolves on next update or sender drop. |
| `Receiver::borrow()` | `Ref<'_, T>` | Borrow current value (does NOT mark as seen). |
| `Receiver::borrow_and_update()` | `Ref<'_, T>` | Borrow and mark as seen. |
| `Receiver::wait_for(F)` | `async, Result<Ref<'_, T>, RecvError>` | Wait until predicate is true. |

ALWAYS pick `watch` for "current configuration", "current health status", "latest sensor reading". NEVER use `broadcast` for these: subscribers join, miss values, and you do not actually care about every intermediate value.

NEVER hold a `borrow()` across `.await`: it is a read-lock guard and blocks updates. Clone the value out, then drop the guard, then await.

---

## crossbeam-channel and flume: alternatives

**crossbeam-channel** ([crossbeam-channel]) is a lock-free **synchronous** mpmc channel. It is faster than `std::sync::mpsc` and supports a native `select!` macro for waiting on multiple channels. It is **not** an `async` channel: receivers block the thread.

```rust
use crossbeam_channel::{bounded, select};

let (tx1, rx1) = bounded::<u32>(16);
let (tx2, rx2) = bounded::<u32>(16);

select! {
    recv(rx1) -> msg => println!("from 1: {msg:?}"),
    recv(rx2) -> msg => println!("from 2: {msg:?}"),
    default(std::time::Duration::from_millis(100)) => println!("timeout"),
}
```

**flume** ([flume]) is an alternative channel crate that supports **both** sync and async receivers from the same channel, plus its own `select!` macro. Useful for code paths that mix threads and async tasks on the same queue.

ALWAYS choose `tokio::sync::mpsc` first in async code (matches the runtime, minimal dependencies). Choose `crossbeam-channel` when you need lock-free sync mpmc or a sync `select!`. Choose `flume` when the **same** channel must be read from threads and async tasks.

NEVER reach for these crates "for performance" without measuring: `tokio::sync::mpsc` is fast enough for the vast majority of workloads, and an extra crate buys complexity.

---

## Backpressure: bounded vs unbounded

Backpressure is the property that "fast producers slow down when slow consumers cannot keep up". A bounded channel propagates backpressure through `send().await` (async) or blocking `send()` (sync, `sync_channel`). An unbounded channel does not propagate backpressure: fast producers queue forever and memory grows unbounded.

| Channel | Backpressure mechanism |
|---------|------------------------|
| `std::sync::mpsc::channel()` | None (unbounded). Memory may grow without bound. |
| `std::sync::mpsc::sync_channel(n)` | Blocks the **thread** on `send` when full. |
| `tokio::sync::mpsc::channel(n)` | Suspends the **task** on `send().await` when full. |
| `tokio::sync::mpsc::unbounded_channel()` | None. Memory may grow without bound. |
| `tokio::sync::broadcast::channel(n)` | None on `send`; consumers receive `Lagged` instead. |
| `tokio::sync::watch::channel(_)` | Coalescing: intermediate values dropped, only latest survives. |

`try_send` vs `send().await` (or blocking `send`) is the next decision:

| Goal | API |
|------|-----|
| Block until queued, accepting latency. | `mpsc::Sender::send(v).await` |
| Drop the message if full, do something else (log, sample). | `mpsc::Sender::try_send(v)` |
| Detect that the consumer is gone and stop producing. | Inspect the `SendError` from either. |

ALWAYS use `send().await` (or bounded sync `send`) by default: that is what backpressure means. ALWAYS use `try_send` only when "drop the value" is a valid policy (telemetry sampling, redraw frames).

---

## SendError and channel-closed signals

A `SendError` always means **"the receiver is gone"** (last `Receiver` was dropped). It is a normal life-cycle event, not a bug:

- For `mpsc`: the consumer task panicked or exited cleanly.
- For `oneshot`: the requester gave up waiting (timed out, cancelled).
- For `broadcast`: all subscribers dropped.
- For `watch`: all watchers dropped.

ALWAYS treat `SendError` as "stop producing, shut down gracefully". NEVER panic on it: the consumer is allowed to leave. Common idioms: `if tx.send(v).await.is_err() { break; }` in a producer loop, or `let _ = resp.send(v);` for fire-and-forget replies.

Symmetrically, the receiver side detects "all producers are gone":

- `mpsc::Receiver::recv()` returns `None`.
- `oneshot::Receiver` resolves with `Err(RecvError)`.
- `broadcast::Receiver::recv()` returns `Err(RecvError::Closed)`.
- `watch::Receiver::changed()` returns `Err(RecvError)`.

ALWAYS use the receiver side to drive shutdown loops: `while let Some(msg) = rx.recv().await { ... }` exits cleanly when every sender has dropped.

---

## Quick reference table

| Pattern | Channel | Bounded? |
|---------|---------|----------|
| Thread-to-thread queue, no Tokio. | `std::sync::mpsc::sync_channel(n)` | yes |
| Thread-to-thread, lock-free, with select. | `crossbeam_channel::bounded(n)` | yes |
| Async task to async task, one consumer. | `tokio::sync::mpsc::channel(n)` | yes |
| Request -> single reply (RPC). | `tokio::sync::oneshot::channel()` | n/a |
| Pub/sub, all subscribers get every event. | `tokio::sync::broadcast::channel(n)` | ring n |
| Latest config / state to many watchers. | `tokio::sync::watch::channel(init)` | overwrites |
| Same queue read by threads and tasks. | `flume::bounded(n)` | yes |
| Multi-producer, multi-consumer, sync. | `crossbeam_channel::unbounded()` / `bounded(n)` | optional |

| Symbol | Origin | One-line meaning |
|--------|--------|------------------|
| `Sender<T>` | `std::sync::mpsc` / `tokio::sync::mpsc` / etc. | The producing half. Clone for multi-producer. |
| `Receiver<T>` | same | The consuming half. Single owner (mpsc) or many (broadcast / watch). |
| `SendError<T>` | every channel | All receivers dropped; the value is returned. |
| `RecvError` | every channel | All senders dropped; for broadcast also `Lagged(n)`. |
| `TrySendError<T>` | every channel | `Full(v)` or `Closed(v)`. |
| `TryRecvError` | every channel | `Empty` or `Disconnected` / `Closed`. |

---

## Avoid these mistakes

(Full list with WHYs in `references/anti-patterns.md`.)

- Calling `std::sync::mpsc::Receiver::recv()` inside an async function: it blocks the OS thread, stalling the Tokio worker. Use `tokio::sync::mpsc` or wrap in `spawn_blocking`.
- Using `tokio::sync::mpsc::unbounded_channel()` because `send().await` "is in the way": removing backpressure trades a slowdown for unbounded memory growth and OOM.
- Reaching for `mpsc::channel(1)` to model "exactly one reply": semantics are wrong (the receiver can drain repeatedly) and a second `send` would queue silently. Use `oneshot`.
- Treating `SendError` as a bug and `.unwrap()`-ing it: the receiver dropping is a normal end-of-life signal. Match on it and shut the producer down.
- Forgetting to drop the original `Sender` after cloning, then waiting forever on `Receiver::recv()`: with one `Sender` still alive `recv` cannot return `None`.
- Holding a `watch::Receiver::borrow()` across `.await`: it is a read-lock that blocks updates, leading to apparent deadlock when the sender tries to publish.
- Ignoring `broadcast::RecvError::Lagged`: consumer silently loses messages with no diagnostic. Always match the variant explicitly.
- Using `broadcast` for "current configuration" and `watch` for "every event": semantics inverted. Broadcast is *every* event, watch is *latest* value.
- Calling `.recv()` in a busy `loop { match rx.try_recv() { ... } }` polling pattern in async code: burns CPU. Use `.recv().await`.
- Sharing the same channel for unrelated message kinds: a typed enum works but limits backpressure granularity. Prefer one channel per logical stream.

---

## Reference links

[std-mpsc]: https://doc.rust-lang.org/std/sync/mpsc/index.html
[tokio-sync]: https://docs.rs/tokio/latest/tokio/sync/index.html
[tokio-mpsc]: https://docs.rs/tokio/latest/tokio/sync/mpsc/index.html
[tokio-mpsc-unbounded]: https://docs.rs/tokio/latest/tokio/sync/mpsc/fn.unbounded_channel.html
[tokio-oneshot]: https://docs.rs/tokio/latest/tokio/sync/oneshot/index.html
[tokio-broadcast]: https://docs.rs/tokio/latest/tokio/sync/broadcast/index.html
[tokio-watch]: https://docs.rs/tokio/latest/tokio/sync/watch/index.html
[crossbeam-channel]: https://docs.rs/crossbeam-channel/latest/crossbeam_channel/
[flume]: https://docs.rs/flume/latest/flume/
[tokio-tutorial-channels]: https://tokio.rs/tokio/tutorial/channels

- [`std::sync::mpsc`][std-mpsc]: stdlib synchronous mpsc, both `channel()` and `sync_channel(n)`.
- [`tokio::sync`][tokio-sync]: index of all async sync primitives.
- [`tokio::sync::mpsc`][tokio-mpsc]: bounded and unbounded async mpsc.
- [`tokio::sync::oneshot`][tokio-oneshot]: single-value channel for request / response.
- [`tokio::sync::broadcast`][tokio-broadcast]: fan-out with `Lagged` semantics.
- [`tokio::sync::watch`][tokio-watch]: latest-value, coalescing.
- [Tokio tutorial : channels][tokio-tutorial-channels]: actor-style mpsc + oneshot pattern.
- [`crossbeam-channel`][crossbeam-channel]: lock-free sync channel with `select!`.
- [`flume`][flume]: sync + async hybrid channel with `select!`.

For deeper drill-downs see:

- `references/methods.md`: complete method signatures for every channel type plus a side-by-side `send` / `recv` comparison.
- `references/examples.md`: working code: actor with mpsc + oneshot, broadcast with Lagged handling, watch for config reload, bounded backpressure demo, crossbeam select!.
- `references/anti-patterns.md`: 10+ documented anti-patterns with WHY and FIX.

---
> Source: [Impertio-Studio/Rust-Claude-Skill-Package](https://github.com/Impertio-Studio/Rust-Claude-Skill-Package) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
