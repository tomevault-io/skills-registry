---
name: rust-impl-concurrency
description: > Use when this capability is needed.
metadata:
  author: Impertio-Studio
---

# rust-impl-concurrency

Thread-safe shared mutable state in Rust is a layered choice. From cheapest to most expensive: atomic (single primitive value, lock-free), `RwLock` (many readers OR one writer), `Mutex` (one accessor at a time). Each layer is wrapped in `Arc<T>` so multiple threads can own the same handle. Picking the wrong layer wastes performance or causes correctness bugs.

Cross-references: [[rust-impl-channels]] (channel pattern is an *alternative* to shared-state pattern), [[rust-syntax-smart-pointers]] (Arc, Rc, Cell, RefCell semantics), [[rust-core-memory-model]] (Send/Sync auto-trait rules), [[rust-impl-async-tokio]] (tokio::sync::Mutex for async contexts).

---

## When to use this skill

- User wraps shared state in `Arc<Mutex<T>>` or asks which sync primitive to pick.
- User reads or writes atomic types (`AtomicUsize`, `AtomicBool`, ...) and must choose an `Ordering`.
- User gets a `PoisonError` and asks how to recover.
- User considers `parking_lot::Mutex` vs `std::sync::Mutex`.
- User borrows local data across threads and needs `std::thread::scope` (stabilised 1.63).
- User asks "why am I deadlocking" (lock-ordering question).
- User asks about `thread_local!` storage.
- User asks "can I use `Rc` between threads" (answer: no, use `Arc`).

---

## Decision tree: which primitive

```
Need shared mutable state between threads?
|
+-- Single primitive value (bool, integer, pointer)?
|     -> std::sync::atomic::AtomicBool / AtomicUsize / AtomicPtr  (lock-free, cheapest)
|
+-- Read-heavy, occasional writes (caches, config, routing tables)?
|     -> Arc<RwLock<T>>                                            (many readers OR one writer)
|
+-- Balanced read/write or simple critical section?
|     -> Arc<Mutex<T>>                                             (one accessor at a time)
|
+-- High contention + no need for poisoning semantics?
|     -> Arc<parking_lot::Mutex<T>> or parking_lot::RwLock         (faster, no Result on lock)
|
+-- Send messages instead of sharing state?
      -> see [[rust-impl-channels]]                                (mpsc / crossbeam / tokio)
```

ALWAYS pick the cheapest primitive that fits. NEVER reach for `Mutex` first when an atomic counter suffices. The clippy lint `clippy::mutex_atomic` explicitly flags `Mutex<bool>` and `Mutex<usize>` for this reason.

---

## Arc<Mutex<T>>: the canonical shared-state pattern

```rust
use std::sync::{Arc, Mutex};
use std::thread;

let data = Arc::new(Mutex::new(Vec::<u32>::new()));

let mut handles = vec![];
for i in 0..4 {
    let data = Arc::clone(&data);
    handles.push(thread::spawn(move || {
        let mut guard = data.lock().expect("mutex poisoned");
        guard.push(i);
    }));
}
for h in handles { h.join().unwrap(); }

let final_guard = data.lock().expect("mutex poisoned");
assert_eq!(final_guard.len(), 4);
```

Verified signatures (`std::sync::Mutex`):

```rust
pub const fn new(t: T) -> Mutex<T>
pub fn lock(&self) -> LockResult<MutexGuard<'_, T>>
pub fn try_lock(&self) -> TryLockResult<MutexGuard<'_, T>>
pub fn into_inner(self) -> LockResult<T> where T: Sized
pub fn get_mut(&mut self) -> LockResult<&mut T>

// LockResult<T>    = Result<T, PoisonError<T>>
// TryLockResult<T> = Result<T, TryLockError<T>>
// TryLockError     = Poisoned(PoisonError<T>) | WouldBlock
```

Rules:

- ALWAYS clone the `Arc`, not the `Mutex`. `Arc::clone(&handle)` is cheap (one atomic increment).
- ALWAYS drop the `MutexGuard` as soon as possible. Holding it across `.await`, across a long compute, or across an I/O call kills throughput.
- NEVER call `.lock()` again on the same `Mutex` while you already hold its guard. `std::sync::Mutex` is NOT reentrant; this deadlocks.
- NEVER hold a `MutexGuard` across an `.await` point in async code. The guard is `!Send` for tokio multi-thread runtime. Use `tokio::sync::Mutex` for async or scope the guard to a non-async block.

---

## Mutex poisoning: what it is, how to recover

A mutex is **poisoned** if a thread panics while holding its guard. After that, every `.lock()` returns `Err(PoisonError<MutexGuard<T>>)`. The data is still reachable; poisoning is *advisory*, it does not corrupt anything by itself.

```rust
use std::sync::{Arc, Mutex};
use std::thread;

let m = Arc::new(Mutex::new(0u32));
let m2 = Arc::clone(&m);

let _ = thread::spawn(move || {
    let mut g = m2.lock().unwrap();
    *g = 42;
    panic!("bad day");  // <- poisons the mutex
}).join();

match m.lock() {
    Ok(g)  => println!("clean: {}", *g),
    Err(p) => {
        // Recover the guard despite poisoning. We choose to trust the data.
        let g = p.into_inner();
        println!("poisoned but recovered: {}", *g);
    }
}
```

ALWAYS decide explicitly: do you trust the partially-mutated state (`p.into_inner()`), or do you reset to a known good value (`*p.into_inner() = default()`), or do you propagate the failure (return `Err` from the caller)? NEVER `.unwrap()` blindly in code where panic recovery is required; pick a recovery strategy. NEVER ignore poisoning in code where invariants must hold: a poisoned lock is a *signal* that some invariant may be broken.

Verified rules:
- `Mutex<T>` becomes poisoned only when a thread panics while holding the lock.
- `RwLock<T>` is poisoned **only on write-lock panic**. Reader panics do NOT poison the lock.
- `parking_lot::Mutex` and `parking_lot::RwLock` do NOT track poisoning at all; `.lock()` returns the guard directly (no `Result`).

---

## RwLock: many readers OR one writer

```rust
use std::sync::{Arc, RwLock};
use std::thread;

let cache = Arc::new(RwLock::new(std::collections::HashMap::<String, u32>::new()));

// Many readers
for i in 0..8 {
    let cache = Arc::clone(&cache);
    thread::spawn(move || {
        let r = cache.read().expect("rwlock write-poisoned");
        if let Some(v) = r.get("hits") { println!("reader {i}: {v}"); }
    });
}

// One writer (blocks all readers)
{
    let mut w = cache.write().expect("rwlock write-poisoned");
    w.insert("hits".to_string(), 1);
}
```

Verified signatures (`std::sync::RwLock`):

```rust
pub fn read(&self) -> LockResult<RwLockReadGuard<'_, T>>
pub fn write(&self) -> LockResult<RwLockWriteGuard<'_, T>>
pub fn try_read(&self) -> TryLockResult<RwLockReadGuard<'_, T>>
pub fn try_write(&self) -> TryLockResult<RwLockWriteGuard<'_, T>>
```

ALWAYS pick `RwLock` over `Mutex` when reads vastly outnumber writes (>10:1 is a good rule of thumb). NEVER assume reader-preference or writer-preference: "The priority policy of the lock is dependent on the underlying operating system's implementation, and this type does not guarantee that any particular policy will be used" (`std::sync::RwLock` docs). On Linux glibc this means writer starvation is possible under sustained reader load.

Deadlock trap: a thread that already holds a read guard and then asks for another `read()` may block forever if a writer is waiting in between. NEVER re-enter the same `RwLock` from one thread; restructure the code.

---

## Atomics: lock-free state for primitives

```rust
use std::sync::atomic::{AtomicUsize, AtomicBool, Ordering};
use std::sync::Arc;
use std::thread;

let counter = Arc::new(AtomicUsize::new(0));
let stop    = Arc::new(AtomicBool::new(false));

let workers: Vec<_> = (0..4).map(|_| {
    let counter = Arc::clone(&counter);
    let stop    = Arc::clone(&stop);
    thread::spawn(move || {
        while !stop.load(Ordering::Acquire) {
            counter.fetch_add(1, Ordering::Relaxed);
        }
    })
}).collect();

thread::sleep(std::time::Duration::from_millis(50));
stop.store(true, Ordering::Release);
for w in workers { w.join().unwrap(); }
println!("counted {}", counter.load(Ordering::Relaxed));
```

Verified atomic types: `AtomicBool`, `AtomicI8`/`I16`/`I32`/`I64`/`Isize`, `AtomicU8`/`U16`/`U32`/`U64`/`Usize`, `AtomicPtr<T>`. See [references/methods.md](references/methods.md) for the full method matrix.

`Ordering` cheat-sheet (definitive memory-model reference is `std::sync::atomic`):

| Variant | Meaning | When to use |
|---------|---------|-------------|
| `Relaxed` | No ordering, just atomicity. | Counters, statistics where order does not matter. |
| `Acquire` | On `load`: prevents reordering of *later* reads/writes before this load. Pairs with `Release` store. | Reading a flag that gates *protected* data. |
| `Release` | On `store`: prevents reordering of *earlier* reads/writes after this store. Pairs with `Acquire` load. | Publishing protected data, then setting a "ready" flag. |
| `AcqRel` | Both, for read-modify-write ops (`fetch_add`, `compare_exchange`). | RMW that both publishes and observes. |
| `SeqCst` | Total order across ALL `SeqCst` operations system-wide. | When you cannot reason about Acquire/Release pairings; the conservative default. |

ALWAYS start with `SeqCst` if you are not 100% confident in the happens-before reasoning. The overhead is real on weak-memory architectures (ARM, RISC-V) but correctness comes first. NEVER use `Relaxed` for synchronisation between threads; `Relaxed` only guarantees that the integer itself is not torn. NEVER pair `Acquire` with `Acquire` (no happens-before edge) or `Release` with `Release`; a producer-consumer pair MUST be Release on the producer side and Acquire on the consumer side.

`compare_and_swap` was **deprecated** in 1.50 (replaced by `compare_exchange` and `compare_exchange_weak`). ALWAYS use `compare_exchange_weak` inside loops (spurious failure allowed, generates better code on ARM/LL-SC architectures); use `compare_exchange` for one-shot CAS.

```rust
let v = AtomicUsize::new(0);
let mut current = v.load(Ordering::Relaxed);
loop {
    let next = current + 1;
    match v.compare_exchange_weak(current, next, Ordering::AcqRel, Ordering::Acquire) {
        Ok(_)    => break,
        Err(now) => current = now,
    }
}
```

---

## Scoped threads (1.63+): borrow without 'static

```rust
use std::thread;

let mut numbers = vec![1, 2, 3];

thread::scope(|s| {
    s.spawn(|| {
        // Borrow numbers immutably across thread boundary; no Arc needed.
        println!("first read: {:?}", &numbers);
    });
    s.spawn(|| {
        println!("second read: {:?}", &numbers);
    });
    // All spawned threads automatically joined before `scope` returns.
});

// After scope returns, exclusive access to numbers is back.
numbers.push(4);
```

Verified signature (`std::thread::scope`, stable since 1.63.0):

```rust
pub fn scope<'env, F, T>(f: F) -> T
where
    F: for<'scope> FnOnce(&'scope Scope<'scope, 'env>) -> T,
```

> "All threads spawned within the scope that haven't been manually joined will be automatically joined before this function returns." (`std::thread::scope` documentation)
>
> "Unlike non-scoped threads, scoped threads can borrow non-`'static` data, as the scope guarantees all threads will be joined at the end of the scope."

ALWAYS prefer `thread::scope` over `thread::spawn` when threads only need to borrow data that lives in the spawning function. The compiler proves no `JoinHandle` leak is possible. NEVER manually mix `thread::spawn` with non-`'static` borrows; the borrow checker rejects it for good reason.

---

## thread_local!: per-thread state

```rust
use std::cell::RefCell;

thread_local! {
    static BUFFER: RefCell<Vec<u8>> = RefCell::new(Vec::with_capacity(1024));
}

fn append(byte: u8) {
    BUFFER.with(|b| b.borrow_mut().push(byte));
}
```

ALWAYS reach for `thread_local!` when each thread needs its own scratch space (buffers, RNGs, caches) without coordination. NEVER use it as a substitute for sharing data; the value is created lazily per thread and never observed by other threads.

---

## Lock ordering: the deadlock prevention rule

**Rule**: if any code path acquires multiple locks, ALL code paths must acquire them in the same global order.

```rust
// CORRECT: define a single canonical order: lock_a before lock_b.
fn transfer(from: &Mutex<Account>, to: &Mutex<Account>, amount: u64) {
    // Order by stable identity (e.g. pointer address) so any pair is consistent.
    let (first, second) = if (from as *const _) < (to as *const _) { (from, to) } else { (to, from) };
    let mut a = first.lock().unwrap();
    let mut b = second.lock().unwrap();
    // ... operate on both
}
```

Anti-pattern (deadlock):

```rust
// Thread 1                    | Thread 2
let a = lock_a.lock().unwrap();| let b = lock_b.lock().unwrap();
let b = lock_b.lock().unwrap();| let a = lock_a.lock().unwrap();
// Each waits for the other -> deadlock.
```

ALWAYS document the lock-ordering convention in every module that holds more than one lock. NEVER acquire locks in data-dependent order without an explicit total order (e.g. by pointer address or by integer id). When in doubt, use a single coarser-grained `Mutex` instead of two fine-grained ones.

---

## parking_lot: faster mutexes, no poisoning

`parking_lot` is a third-party crate providing `parking_lot::Mutex`, `parking_lot::RwLock`, `parking_lot::Condvar`, etc. Differences from `std::sync`:

- **No poisoning**: `.lock()` returns a guard directly, NOT a `Result`. Simpler ergonomics; you lose the panic-corruption-signal.
- **Faster on contention**: spin-then-park strategy outperforms `std::sync::Mutex` under contention on many platforms.
- **Smaller**: a `parking_lot::Mutex<()>` is one word; `std::sync::Mutex<()>` is multiple words.
- **`const fn new`** in `parking_lot` 0.12+: usable in `static` items without `OnceLock`/`LazyLock`.
- `RwLock` has explicit fairness/upgrade APIs (`upgradable_read`, `try_upgrade`) that `std::sync::RwLock` lacks.

ALWAYS prefer `std::sync` when the standard library suffices: one fewer dependency, poisoning gives panic-corruption-signal. ALWAYS reach for `parking_lot` when (a) you measured contention and `std::sync` is the bottleneck, (b) you need `Condvar` with non-poisoning semantics, or (c) you need `const`-constructible mutexes in `static`s. NEVER assume `parking_lot::Mutex<T>` is `Send` + `Sync` for every `T`; it follows the same `T: Send` rules as `std::sync::Mutex<T>`.

---

## Send and Sync at a glance

| Type | `Send` | `Sync` | Reason |
|------|--------|--------|--------|
| `Rc<T>` | NO | NO | Non-atomic refcount, races on clone. |
| `Arc<T>` | YES if `T: Send + Sync` | YES if `T: Send + Sync` | Atomic refcount. |
| `Mutex<T>` | YES if `T: Send` | YES if `T: Send` | Mutex serialises access. |
| `RwLock<T>` | YES if `T: Send + Sync` | YES if `T: Send + Sync` | Multiple `&T` exposed by readers. |
| `RefCell<T>` | YES if `T: Send` | NO | Runtime borrow check is not thread-safe. |
| `Cell<T>`    | YES if `T: Send` | NO | Same reason. |
| `*const T` / `*mut T` | NO | NO | Raw pointers opt out. |

ALWAYS use `Arc<Mutex<T>>` for shared mutable state. NEVER use `Arc<RefCell<T>>` (compile-error: `RefCell` is `!Sync`, `Arc` requires `Send + Sync`). NEVER use `Rc<Mutex<T>>` across threads; `Rc` is `!Send`. See [[rust-core-memory-model]] for the full Send/Sync auto-trait rules.

---

## Patterns: see references

- [references/methods.md](references/methods.md) : exact API tables for `Mutex`, `RwLock`, every atomic type, `Ordering`, `thread::scope`, `thread_local!`, `parking_lot::Mutex`.
- [references/examples.md](references/examples.md) : worked examples (counter with atomic, cache with RwLock, work-stealing with scoped threads, recovering from a poisoned lock, CAS loop, `Condvar` wait-on-condition, lock-free flag with `AtomicBool`).
- [references/anti-patterns.md](references/anti-patterns.md) : `Mutex<usize>` for a counter, lock-order deadlock, `.unwrap()` on poisoning, `Relaxed` for synchronisation, holding a guard across a long operation, recursive locking, `Arc<RefCell<T>>` confusion.

---

## Quick rules

1. ALWAYS pick atomic > RwLock > Mutex in that order of preference for shared state.
2. ALWAYS clone the `Arc`, never the inner primitive.
3. ALWAYS drop the lock guard as soon as the critical section ends.
4. ALWAYS define a global lock-ordering when multiple locks are needed.
5. ALWAYS handle `PoisonError` explicitly: recover, reset, or propagate.
6. ALWAYS prefer `thread::scope` over `thread::spawn` for non-`'static` borrows.
7. NEVER hold a `std::sync::MutexGuard` across an `.await` point.
8. NEVER use `Relaxed` ordering for cross-thread synchronisation.
9. NEVER use `Arc<RefCell<T>>` or `Rc<Mutex<T>>`; the auto-trait rules forbid it.
10. NEVER acquire a lock recursively from the same thread with `std::sync::Mutex`.

---

## Verified sources (2026-05-19)

- https://doc.rust-lang.org/std/sync/index.html
- https://doc.rust-lang.org/std/sync/atomic/index.html
- https://doc.rust-lang.org/std/sync/struct.Mutex.html
- https://doc.rust-lang.org/std/sync/struct.RwLock.html
- https://doc.rust-lang.org/std/thread/fn.scope.html

---
> Source: [Impertio-Studio/Rust-Claude-Skill-Package](https://github.com/Impertio-Studio/Rust-Claude-Skill-Package) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
