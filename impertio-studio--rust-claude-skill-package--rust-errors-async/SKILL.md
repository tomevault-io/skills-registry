---
name: rust-errors-async
description: > Use when this capability is needed.
metadata:
  author: Impertio-Studio
---

# rust-errors-async

Decodes the five recurring async error classes and gives one correct fix per class. Async errors are almost never bugs in `async`/`.await` itself: they are ordinary trait, `Send`, lifetime, or runtime-policy violations that surface only because `async fn` desugars into an anonymous state-machine `Future`. The fix targets the underlying violation, not the `async` keyword.

Cross-references: [[rust-syntax-async-await]] (async/await mechanics, desugaring), [[rust-impl-async-tokio]] (Tokio runtime setup, spawn, channels), [[rust-core-async-runtime]] (Future/Pin/Waker model), [[rust-agents-compile-fix]] (automated compile-fix loop).

---

## When to use this skill

- The compiler reports `future cannot be sent between threads safely` or `future is not Send`.
- A `tokio::spawn` call fails with a `Send` bound error.
- The compiler reports `cannot move out of` a pinned value, or `... is not Unpin`.
- A lifetime error appears only inside an `async fn` body or signature.
- The user asks "do I still need the `async_trait` crate".
- The program panics at runtime with `Cannot start a runtime from within a runtime`.
- An async program hangs, stalls, or one slow task freezes every other task.

---

## The one rule behind every async error

> An `async fn` is sugar for a function returning an anonymous `Future`. Every async error is a property of that hidden `Future`: is it `Send`, is it `Unpin`, what lifetimes does it capture, and is it polled by a legal runtime call.

ALWAYS identify which of those four properties failed before proposing a fix. The `async` keyword is never the defect.

---

## Error class routing table

| Symptom | Hidden cause | First fix to try |
|---------|--------------|------------------|
| "future cannot be sent between threads safely" | a `!Send` value is live across an `.await` | scope the value to end before `.await` |
| "cannot move out of ... pinned" / "is not Unpin" | moving a `!Unpin` (self-referential) future | `Box::pin`, `tokio::pin!`, or `std::pin::pin!` |
| lifetime error inside `async fn` | the returned future captures every input lifetime | take owned arguments, or shorten the borrow |
| "async fn in trait" + `dyn Trait` fails | AFIT future is anonymous, unsized, not object-safe | `#[async_trait]` or `-> Pin<Box<dyn Future>>` |
| `Cannot start a runtime from within a runtime` (panic) | nested `Runtime::new().block_on()` | use the current runtime via `Handle`, or `spawn_blocking` |

---

## Class 1: future is not Send

`error: future cannot be sent between threads safely` appears when you `tokio::spawn` (multi-thread runtime) a future that is not `Send`. The spawned future must be `Send + 'static`.

A future is `Send` only if **every value held live across an `.await` point** is `Send`. The desugared state machine stores those values in itself, so one `!Send` value poisons the whole future. The error note always points at the exact line: `... which is not Send` plus `... is later dropped here` after an `.await`.

Common `!Send` offenders held across `.await`:

- `std::sync::MutexGuard` / `RwLockReadGuard` / `RwLockWriteGuard`
- `Rc<T>`, `rc::Weak<T>`
- `RefCell` borrow guards (`Ref<T>`, `RefMut<T>`)
- raw pointers `*const T` / `*mut T`

### Fix A: scope the guard so it drops before `.await` (preferred)

```rust
// FAILS: std MutexGuard is held across .await
async fn bad(data: std::sync::Arc<std::sync::Mutex<i32>>) {
    let guard = data.lock().unwrap();
    do_async_work().await; // guard still alive here -> future is !Send
    println!("{}", *guard);
}

// CORRECT: read the value out, end the guard's scope, then await
async fn good(data: std::sync::Arc<std::sync::Mutex<i32>>) {
    let value = {
        let guard = data.lock().unwrap();
        *guard
    }; // guard dropped here, nothing !Send crosses the .await
    do_async_work().await;
    println!("{}", value);
}
```

### Fix B: use `tokio::sync::Mutex` when the lock MUST stay held across `.await`

`tokio::sync::Mutex` is designed so its guard is `Send`, and `.lock()` is itself `async`. Use it ONLY when the critical section genuinely spans an `.await`; for a quick non-async critical section `std::sync::Mutex` is faster and correct.

```rust
use tokio::sync::Mutex;
use std::sync::Arc;

async fn ok(data: Arc<Mutex<i32>>) {
    let mut guard = data.lock().await; // tokio guard IS Send
    do_async_work().await;             // legal: guard crosses .await
    *guard += 1;
}
```

### Fix C: convert `Rc` to `Arc`

If the `!Send` value is an `Rc<T>`, switch to `Arc<T>`. Do this only when sharing across threads is genuinely needed; if the value never leaves one task, prefer a `current_thread` runtime instead (which does not require `Send`).

NEVER `.clone()` a large structure purely to dodge this error when scoping the guard (Fix A) would work. See `references/anti-patterns.md`.

---

## Class 2: cannot move out of pinned / not Unpin

`async` blocks compile to potentially self-referential state machines, so their `Future` type is `!Unpin`. A `!Unpin` future must not be moved once polling has started; `Pin` enforces that. Errors: `cannot move out of dereference of Pin<...>`, or a function requires `T: Unpin` but the future is not.

The fix is always to pin the future. Choose the pin location:

```rust
use std::future::Future;

// Heap pin: required when the future must outlive the current stack frame,
// or be stored in a struct, Vec, or returned as Pin<Box<dyn Future>>.
fn boxed(fut: impl Future<Output = ()> + 'static) -> std::pin::Pin<Box<dyn Future<Output = ()>>> {
    Box::pin(fut)
}

// Stack pin: cheapest, when the future is used only within this scope.
async fn stack_pin() {
    let fut = some_async_op();
    let mut fut = std::pin::pin!(fut); // std::pin::pin! since Rust 1.68
    poll_helper(fut.as_mut()).await;
}

// tokio::pin! is the same stack-pin tool, common in select! loops.
async fn select_loop() {
    let sleep = tokio::time::sleep(std::time::Duration::from_secs(1));
    tokio::pin!(sleep);
    loop {
        tokio::select! {
            _ = &mut sleep => break,
            _ = work() => {}
        }
    }
}
```

Decision: `std::pin::pin!` / `tokio::pin!` for **stack-local** use (no allocation); `Box::pin` when the future must be **stored, returned, or type-erased**. Pinning a future does NOT make it `Send`; that is Class 1.

NEVER wrap every future in `Box::pin` reflexively to silence pin errors. Most futures only need a stack pin, and `select!` already pins its branches.

---

## Class 3: lifetime errors inside an async fn

An `async fn` returns a `Future` that **captures every lifetime in its argument list**, because the desugared state machine may need those borrows at any later poll. So a borrowed argument keeps the borrow alive for the entire duration of the returned future, not just the synchronous prologue.

```rust
// FAILS: the returned future borrows `text` for its whole lifetime,
// but the caller may drop `text` while the future is still pending.
async fn process(text: &str) -> usize {
    do_async_work().await;
    text.len()
}
// store_for_later(process(&local_string)); // borrow does not live long enough
```

### Fix A: take an owned argument

```rust
async fn process(text: String) -> usize {
    do_async_work().await;
    text.len()
}
```

### Fix B: keep the borrow, but tie the future's lifetime explicitly

When the caller can guarantee the data outlives the future, name the lifetime:

```rust
async fn process<'a>(text: &'a str) -> usize {
    do_async_work().await;
    text.len()
}
// the returned `impl Future + 'a` now visibly borrows for 'a
```

### Fix C: finish the borrow before the first `.await`

If the borrow is only needed synchronously, extract the owned result first:

```rust
async fn process(text: &str) -> usize {
    let len = text.len(); // borrow used and finished here
    do_async_work().await;
    len
}
```

Decision: prefer **owned arguments** (Fix A) for futures that are spawned or stored; keep borrows only for futures awaited immediately in the same scope. See `references/methods.md` for the desugaring detail.

---

## Class 4: async fn in trait and dyn dispatch (AFIT)

Rust 1.75 stabilized `async fn` in traits (AFIT) and `-> impl Trait` in traits (RPITIT). For **static dispatch** (generic `T: MyTrait` or `impl MyTrait`), native `async fn` in a trait works with no crate:

```rust
// WORKS on stable since 1.75 for static dispatch
trait Fetch {
    async fn get(&self, url: &str) -> String;
}

struct Client;
impl Fetch for Client {
    async fn get(&self, url: &str) -> String {
        format!("body of {url}")
    }
}

// static dispatch: monomorphized, no boxing
async fn use_static<F: Fetch>(f: &F) -> String { f.get("x").await }
```

The limitation: a native AFIT method returns an **anonymous, unsized** future, so the trait is **not object-safe** through that method. `dyn Fetch` does NOT compile, and there is no `Send` bound you can write on the AFIT return future at the trait level.

### When `async_trait` is still required

| Need | Use |
|------|-----|
| static dispatch, generic bound or `impl Trait` | native `async fn` in trait, no crate |
| `dyn Trait` object (trait objects, heterogeneous collections) | `#[async_trait]` crate, OR manual `-> Pin<Box<dyn Future + Send>>` |
| a `Send` bound on the returned future for spawning | `#[async_trait]`, OR the `trait-variant` crate's `#[trait_variant::make(... : Send)]` |

```rust
// dyn dispatch path: async_trait boxes the future so the trait is object-safe
#[async_trait::async_trait]
trait Fetch {
    async fn get(&self, url: &str) -> String;
}
// now `Box<dyn Fetch>` and `&dyn Fetch` compile

// Manual equivalent without the crate:
trait FetchManual {
    fn get<'a>(&'a self, url: &'a str)
        -> std::pin::Pin<Box<dyn std::future::Future<Output = String> + Send + 'a>>;
}
```

Decision: use **native AFIT** by default. Reach for `#[async_trait]` ONLY when you genuinely need `dyn Trait` or a trait-level `Send` bound. NEVER add `#[async_trait]` to a trait that is only ever used with generic static dispatch; it forces a heap allocation per call for no reason.

---

## Class 5: blocking the runtime and nested runtimes

These are runtime-policy errors. Some panic; some only manifest as hangs.

### `Cannot start a runtime from within a runtime` (panic)

Calling `tokio::runtime::Runtime::new().unwrap().block_on(...)`, or `futures::executor::block_on`, from inside code already driven by a Tokio runtime panics or deadlocks. You already have a runtime; do not create a second.

```rust
// PANICS: creates a nested runtime inside #[tokio::main]
#[tokio::main]
async fn main() {
    let rt = tokio::runtime::Runtime::new().unwrap();
    rt.block_on(work()); // "Cannot start a runtime from within a runtime"
}

// CORRECT: you are already async, just .await
#[tokio::main]
async fn main() {
    work().await;
}

// From a SYNC function called inside async code, reuse the current runtime:
fn sync_bridge() {
    let handle = tokio::runtime::Handle::current();
    let result = tokio::task::block_in_place(|| handle.block_on(work()));
    let _ = result;
}
```

### Blocking calls starve the executor (hang, no panic)

`std::thread::sleep`, synchronous file/network I/O, `std::sync::Mutex` contention, and CPU-bound loops block the worker thread. The runtime cannot detect this; the symptom is that unrelated tasks stop making progress.

```rust
// BAD: blocks the worker thread, freezes other tasks
async fn bad() {
    std::thread::sleep(std::time::Duration::from_secs(5)); // blocks the executor
    expensive_cpu_loop();                                  // also blocks
}

// CORRECT: async sleep yields; CPU work goes to the blocking pool
async fn good() {
    tokio::time::sleep(std::time::Duration::from_secs(5)).await;
    let result = tokio::task::spawn_blocking(|| expensive_cpu_loop()).await.unwrap();
    let _ = result;
}
```

Decision: `tokio::time::sleep` and `tokio::fs` for async-native equivalents; `spawn_blocking` for synchronous third-party I/O and CPU-bound work; `block_in_place` ONLY when the blocking work must stay on the current task and the runtime is multi-thread (it panics on a `current_thread` runtime). NEVER run a CPU-bound loop directly on an async worker.

---

## Fix-selection decision tree

```
Async error?
├─ "future cannot be sent between threads safely" (Class 1)
│   ├─ value only needed before .await   -> scope it in a { } block
│   ├─ lock MUST span the .await         -> tokio::sync::Mutex
│   └─ value is an Rc shared cross-task  -> Arc, or current_thread runtime
├─ "cannot move out of pinned" / "not Unpin" (Class 2)
│   ├─ future used only in this scope    -> std::pin::pin! / tokio::pin!
│   └─ future stored / returned / erased -> Box::pin
├─ lifetime error inside async fn (Class 3)
│   ├─ future is spawned or stored       -> take owned arguments
│   └─ future awaited immediately        -> name 'a, or finish borrow pre-.await
├─ async fn in trait fails (Class 4)
│   ├─ generic / impl Trait dispatch     -> native AFIT, no crate
│   └─ dyn Trait or trait-level Send     -> #[async_trait] or Pin<Box<dyn Future>>
└─ runtime panic / hang (Class 5)
    ├─ "runtime from within a runtime"   -> .await directly, or Handle::current()
    └─ tasks freeze, no panic            -> spawn_blocking / tokio::time::sleep
```

---

## Reference files

- `references/methods.md`: the `Future` trait and `poll` contract, `Pin`/`Unpin` invariants, why `async fn` captures all input lifetimes, the `Send + 'static` spawn bound, `tokio::sync::Mutex` vs `std::sync::Mutex`, AFIT vs RPITIT object-safety rules, `block_in_place` / `spawn_blocking` / `Handle` API surface.
- `references/examples.md`: full failing-then-fixed programs for every class, including the MutexGuard-across-await note output, the `select!` pin loop, the AFIT static-vs-dyn split, and the nested-runtime panic with its fix.
- `references/anti-patterns.md`: holding `std::sync::MutexGuard` across `.await`, reflexive `Box::pin`, `#[async_trait]` on static-only traits, `block_on` inside async context, CPU-bound work on async workers, cloning huge data to dodge a `Send` error.

## Sources

- Async Book: https://rust-lang.github.io/async-book/
- std::future::Future: https://doc.rust-lang.org/std/future/trait.Future.html
- std::pin (Pin/Unpin invariants): https://doc.rust-lang.org/std/pin/index.html
- Rust 1.75 (AFIT / RPITIT stabilization): https://blog.rust-lang.org/2023/12/28/Rust-1.75.0/
- Tokio documentation: https://docs.rs/tokio/latest/tokio/
- tokio::sync::Mutex: https://docs.rs/tokio/latest/tokio/sync/struct.Mutex.html
- tokio::task::block_in_place: https://docs.rs/tokio/latest/tokio/task/fn.block_in_place.html
- tokio::task::spawn_blocking: https://docs.rs/tokio/latest/tokio/task/fn.spawn_blocking.html

---
> Source: [Impertio-Studio/Rust-Claude-Skill-Package](https://github.com/Impertio-Studio/Rust-Claude-Skill-Package) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
