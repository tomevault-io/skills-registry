---
name: rust-concurrency
description: Detect and fix concurrency bugs in Rust: data races prevented by Send/Sync, deadlocks from inconsistent lock ordering, poisoned Mutex after panic, MutexGuard held across .await (deadlock + !Send future), Rc/RefCell in spawned or async contexts, blocking calls inside async without spawn_blocking, Arc<Mutex> where the value is never mutated, Cell/RefCell shared across threads. Covers std threads, JoinHandle, move closures, mpsc channels, Arc<Mutex<T>>, Arc<RwLock<T>>, Send/Sync marker traits, lock poisoning recovery, and tokio-specific async caveats. Auto-triggers on: Arc<Mutex, lock().unwrap(), std::sync in async fn, Rc across spawn, MutexGuard held across .await, thread::sleep inside async, spawn_blocking missing. Use when this capability is needed.
metadata:
  author: adelabdelgawad
---

# Fearless Concurrency (+ Async Caveats)

Rust's ownership and type system makes many concurrency bugs **compile-time errors** rather than runtime surprises. The Book states it directly: "By leveraging ownership and type checking, many concurrency errors are compile-time errors in Rust rather than runtime errors." This skill covers the patterns the compiler enforces, the patterns it cannot enforce (deadlocks, poisoning, blocking-in-async), and the async-runtime caveats that appear on top.

---

## When to Use

Invoke this skill when reviewing or writing code that:

- Introduces `Arc<Mutex<T>>` or `Arc<RwLock<T>>` shared state
- Holds a `std::sync::MutexGuard` and calls `.await` in the same block
- Uses `Rc` or `RefCell` in code passed to `thread::spawn` or `tokio::spawn`
- Calls `thread::sleep`, blocking file I/O, or CPU-intensive work inside an `async fn`
- Acquires two or more distinct locks in the same function (deadlock risk)
- Uses `.lock().unwrap()` in long-lived, multi-threaded code paths
- Uses `Cell` or `RefCell` wrapped in `Arc`
- Wraps an immutable value in `Arc<Mutex<T>>` with no write path

---

## Core Idioms

```rust
// ✅ Shared mutable state: Arc<Mutex<T>>
let counter = Arc::new(Mutex::new(0u64));
let c = Arc::clone(&counter);
thread::spawn(move || {
    // Production code: handle poisoning — see Forbidden 4 for the full pattern.
    *c.lock().unwrap_or_else(|p| p.into_inner()) += 1;
});

// ✅ Ownership transfer via channel — send() moves the value; no double-use possible
// (illustrative only: in production propagate the SendError rather than unwrapping)
let (tx, rx) = mpsc::channel::<String>();
thread::spawn(move || {
    let msg = String::from("hello");
    let _ = tx.send(msg); // SendError means receiver dropped; log or ignore per context
    // println!("{msg}");  // ← compile error: value moved into channel
});

// ✅ Drop the guard BEFORE .await — prevents !Send future and deadlock
async fn update(state: Arc<Mutex<HashMap<u32, String>>>, k: u32) {
    {
        // Production code: handle poisoning — see Forbidden 4 for the full pattern.
        let mut map = state.lock().unwrap_or_else(|p| p.into_inner());
        map.insert(k, "pending".into());
    }                          // guard dropped here
    do_async_work().await;     // .await is now safe
}

// ✅ tokio::sync::Mutex when the guard genuinely must span an await point
use tokio::sync::Mutex as AsyncMutex;
async fn fetch_and_store(state: Arc<AsyncMutex<Vec<u8>>>) {
    let mut v = state.lock().await;
    let bytes = fetch_bytes().await;   // guard still held — intentional, tokio-safe
    v.extend(bytes);
}

// ✅ Read-heavy shared state: Arc<RwLock<T>> allows N concurrent readers
let config = Arc::new(RwLock::new(Config::default()));
let reader = Arc::clone(&config);
thread::spawn(move || {
    // Production code: handle poisoning — see Forbidden 4 for the full pattern.
    let cfg = reader.read().unwrap_or_else(|p| p.into_inner());
    println!("{}", cfg.timeout);
});
```

---

## Forbidden Patterns

### Forbidden 1 — `std::sync::MutexGuard` Held Across `.await`

```rust
// ❌ guard lives across the await point
async fn bad(state: Arc<Mutex<Vec<u8>>>) {
    let mut v = state.lock().unwrap(); // MutexGuard is !Send
    some_async_call().await;           // future becomes !Send → compile error on multi-thread runtime
    v.push(1);
}

// ✅ Option A: drop the guard before await
async fn good_a(state: Arc<Mutex<Vec<u8>>>) {
    {
        // Production code: handle poisoning — see Forbidden 4 for the full pattern.
        let mut v = state.lock().unwrap_or_else(|p| p.into_inner());
        v.push(1);
    }
    some_async_call().await;
}

// ✅ Option B: use tokio::sync::Mutex when you need the guard to span the await
use tokio::sync::Mutex as TokioMutex;
async fn good_b(state: Arc<TokioMutex<Vec<u8>>>) {
    let mut v = state.lock().await;
    some_async_call().await;
    v.push(1);
}
```

**Why:** `std::sync::MutexGuard<T>` is `!Send`. Tokio's multi-threaded runtime requires futures passed to `spawn` to be `Send`. Holding a `std` guard across `.await` makes the enclosing future `!Send`, causing a compile error on `tokio::spawn`. Even if the runtime is single-threaded today, the pattern is fragile and prohibits future migration.

```bash
# Detector — lock() call sites in async fns; confirm no .await follows in the same block
grep -rn '\.lock()' src/ | grep -vE 'tokio::sync|async_mutex'
# heuristic — matches all .lock() calls including legitimate std Mutex outside async; rustc/clippy is the real gate
```

---

### Forbidden 2 — `Rc` or `RefCell` in Code That Must Be `Send`

```rust
// ❌ Rc is !Send — cannot move across thread boundary
let data = Rc::new(vec![1, 2, 3]);
thread::spawn(move || println!("{data:?}")); // compile error: Rc<Vec<i32>>: !Send

// ❌ Rc across .await — spawned future must be Send
async fn bad(data: Rc<Config>) {
    expensive_call().await; // Rc<Config>: !Send → tokio::spawn rejects this future
}

// ✅ Use Arc instead of Rc wherever the value crosses thread or await boundaries
let data = Arc::new(vec![1, 2, 3]);
thread::spawn(move || println!("{data:?}")); // fine: Arc<Vec<i32>>: Send
```

**Why:** The Book explains: "This cannot implement `Send` because if you cloned an `Rc<T>` value and tried to transfer ownership of the clone to another thread, both threads might update the reference count at the same time." `Arc<T>` uses atomic operations and is `Send + Sync`. (Book ch16-04)

```bash
# Detector
grep -rnE 'Rc::new|Rc<' src/ | grep -vE '#\[cfg\(test|// @single-thread'
grep -rnE 'RefCell::new|RefCell<' src/ | grep -vE '#\[cfg\(test|// @single-thread'
# heuristic — matches all Rc/RefCell usage including legitimate WASM/single-thread code;
# flag only hits in files that also contain thread::spawn or tokio::spawn; rustc/clippy is the real gate
```

---

### Forbidden 3 — Inconsistent Lock Ordering (Deadlock)

```rust
// ❌ Thread A: locks accounts then limits.
// ❌ Thread B: locks limits then accounts.
// Both threads block waiting for the other → classic deadlock.
async fn transfer(accounts: Arc<Mutex<Accounts>>, limits: Arc<Mutex<Limits>>) {
    let _a = accounts.lock().unwrap();
    let _l = limits.lock().unwrap(); // deadlock if another task acquired limits first
}

// ✅ Establish a global acquisition order (alphabetical, by memory address, by ID)
// and enforce it at every call site.
fn acquire_both<'a>(
    first: &'a Mutex<Resource>,
    second: &'a Mutex<Resource>,
) -> (MutexGuard<'a, Resource>, MutexGuard<'a, Resource>) {
    // Production code: handle poisoning — see Forbidden 4 for the full pattern.
    let g1 = first.lock().unwrap_or_else(|p| p.into_inner());
    let g2 = second.lock().unwrap_or_else(|p| p.into_inner());
    (g1, g2)
}
// Callers always pass (lower_id, higher_id) — consistent order prevents deadlock.
```

**Why:** The Book warns: "Rust can't protect you from all kinds of logic errors when you use `Mutex<T>`… `Mutex<T>` comes with the risk of creating deadlocks. These occur when an operation needs to lock two resources and two threads have each acquired one of the locks, causing them to wait for each other forever." (Book ch16-03) This is a logic-level invariant; the compiler has no visibility into runtime acquisition order.

```bash
# Heuristic detector — files with more than one .lock() call site (function-granular
# detection is not possible in a shell one-liner; use this as a review trigger)
grep -rn '\.lock()' src/ | awk -F: '{print $1}' | sort | uniq -d
# heuristic — file-granular, not function-granular; every match requires manual inspection
# to determine whether two locks are acquired in the same scope; rustc/clippy is the real gate
```

---

### Forbidden 4 — `.lock().unwrap()` Without Considering Poisoning

```rust
// ❌ Ignores the poisoned-lock case silently
let val = shared.lock().unwrap(); // panics if any previous holder panicked

// ✅ Option A: recover the inner value from a poisoned lock (often correct for long-lived state)
let val = shared.lock().unwrap_or_else(|poisoned| {
    tracing::warn!("mutex was poisoned; recovering inner value");
    poisoned.into_inner()
});

// ✅ Option B: propagate as an explicit error
let val = shared.lock().map_err(|_| AppError::Internal)?;
```

**Why:** The std library docs note that a mutex becomes poisoned if the thread holding it panics. Once poisoned, all other threads are unable to access the data by default, and subsequent `lock()` calls return `Err(PoisonError<...>)` ([std::sync::Mutex §Poisoning](https://doc.rust-lang.org/std/sync/struct.Mutex.html#poisoning)). The Book only says "The call to `lock` would fail if another thread holding the lock panicked" — the `PoisonError` API details are documented in std, not the Book chapter. Blindly calling `.unwrap()` turns an upstream panic into a cascade of panics across every thread that touches the mutex. `.unwrap_or_else(|p| p.into_inner())` recovers the data; the poison flag signals that the invariant may be broken, so log and audit before trusting the recovered value.

```bash
# Detector — unwrap on a lock result without poison recovery
grep -rn '\.lock()\.unwrap()' src/ | grep -vE '#\[cfg\(test'
# heuristic — misses .lock() and .unwrap() on separate lines; rustc/clippy is the real gate
```

---

### Forbidden 5 — Blocking Call Inside Async Without `spawn_blocking`

```rust
// ❌ Blocks the entire async executor thread
async fn hash_password(password: String) -> String {
    std::thread::sleep(Duration::from_secs(1)); // blocks executor
    bcrypt::hash(&password, 12).unwrap()        // CPU-intensive: blocks executor
}

// ✅ Offload blocking/CPU work to the blocking thread pool
async fn hash_password(password: String) -> Result<String, AppError> {
    tokio::task::spawn_blocking(move || {
        bcrypt::hash(&password, 12)
    })
    .await
    .map_err(|_| AppError::Internal)?
    .map_err(|_| AppError::Internal)
}
```

**Why:** Async runtimes like Tokio multiplex many tasks onto a small pool of OS threads. A blocking call (`std::thread::sleep`, blocking file I/O, heavy CPU computation) inside an `async fn` starves every other task scheduled on that thread. `spawn_blocking` moves the work to a dedicated blocking thread pool sized for blocking workloads.

```bash
# Detector — blocking calls or CPU-heavy crates called directly inside async fns
grep -rnE 'thread::sleep|std::fs::|bcrypt::|argon2::' src/ \
  | grep -vE 'spawn_blocking|#\[cfg\(test'
# heuristic — matches any use of these APIs even in non-async contexts or inside spawn_blocking;
# confirm the match is inside an async fn body before treating as a violation; rustc/clippy is the real gate
```

---

### Forbidden 6 — `Cell` or `RefCell` Shared Across Threads

```rust
// ❌ Cell<T> is !Sync — cannot share a &Cell<T> across threads
let cell = Arc::new(Cell::new(0u32));
thread::spawn(move || cell.set(1)); // compile error: Cell<u32>: !Sync

// ❌ RefCell<T> is !Sync for the same reason
let shared = Arc::new(RefCell::new(vec![]));
thread::spawn(move || shared.borrow_mut().push(1)); // compile error
```

**Why:** `Cell<T>` and `RefCell<T>` provide interior mutability without synchronization. They are `!Sync`, so `Arc<Cell<T>>` does not implement `Send`. The compiler rejects the code, but the pattern surfaces in refactors where someone wraps a single-threaded type in `Arc` without changing the interior mutability strategy. Correct replacement: `Arc<Mutex<T>>` or `Arc<RwLock<T>>`.

```bash
# Detector — matches both type-annotation form and constructor call form
grep -rnE 'Arc<.*(Ref)?Cell<|Arc::new\((Cell|RefCell)::new' src/
# heuristic — constructor-form matches (Arc::new(Cell::new(…))) and annotation-form matches;
# review each to confirm the inner type is not wrapped in a synchronization primitive; rustc/clippy is the real gate
```

---

### Forbidden 7 — `Arc<Mutex<T>>` When the Value Is Never Mutated

```rust
// ❌ Mutex overhead with no mutation — forces exclusive lock even for reads
let config = Arc::new(Mutex::new(AppConfig::load()));
let cfg = config.lock().unwrap(); // exclusive lock for a read-only value

// ✅ Option A: if truly immutable after construction, just use Arc<T>
let config = Arc::new(AppConfig::load());
// multiple threads share &AppConfig freely — no lock needed

// ✅ Option B: if writes are rare but reads are frequent, use Arc<RwLock<T>>
let config = Arc::new(RwLock::new(AppConfig::load()));
// Production code: handle poisoning — see Forbidden 4 for the full pattern.
let cfg = config.read().unwrap_or_else(|p| p.into_inner()); // concurrent readers; exclusive only for writes
```

**Why:** `Mutex<T>` serializes all access — readers block each other. When data is constructed once and then only read, `Arc<T>` (zero lock overhead) is correct. When reads dominate but occasional writes happen, `Arc<RwLock<T>>` lets N readers proceed concurrently while a writer gets exclusive access.

```bash
# Heuristic detector — Arc<Mutex<T>> fields with no .lock()...= assignment in the codebase
# (manual review trigger; automated detection is approximate)
# Matches both annotation form (Arc<Mutex<) and constructor call form (Arc::new(Mutex::new))
grep -rnE 'Arc<Mutex<|Arc::new\(Mutex::new' src/
# Then grep for mutation patterns to verify at least one write path exists:
grep -rnE '\.lock\(\).*=|\.lock\(\).*push|\.lock\(\).*insert|\.lock\(\).*remove' src/
# heuristic — constructor-form misses type aliases; annotation-form misses fully constructor-built values;
# cross-reference both outputs manually; rustc/clippy is the real gate
```

---

## Async/Tokio Caveats Section

The patterns above apply to `std` threads. Async runtimes add a second layer of rules.

| Scenario | std threads | tokio::spawn |
|---|---|---|
| `Rc<T>` captured | compile error (not `Send`) | compile error (future not `Send`) |
| `std::sync::MutexGuard` across await | n/a | compile error (not `Send`) |
| blocking call in task | starves thread pool | starves async executor |
| `tokio::sync::Mutex` guard across await | n/a | OK — designed for this |

**Rule of thumb:** If a future is passed to `tokio::spawn`, every value it holds across an `.await` must be `Send`. The compiler enforces this. The human-review gap is *which `.await`* a value crosses — check each lock scope manually.

**Cross-reference:** `leptos-hydration-discipline` Forbidden 9 covers the identical `!Send` error for `#[server]` functions specifically (Rc, RefCell, raw PgConnection across await). That skill is the project-specific application of this general rule.

---

## Book References

All std-thread material is grounded in The Rust Programming Language (official edition, doc.rust-lang.org/book):

- **Ch 16 intro** — "Fearless Concurrency": *"By leveraging ownership and type checking, many concurrency errors are compile-time errors in Rust rather than runtime errors."*
  <https://doc.rust-lang.org/book/ch16-00-concurrency.html>

- **Ch 16.1** — "Using Threads to Run Code Simultaneously": `thread::spawn`, `JoinHandle::join`, `move` closures. *"By adding the `move` keyword before the closure, we force the closure to take ownership of the values it's using rather than allowing Rust to infer that it should borrow the values."*
  <https://doc.rust-lang.org/book/ch16-01-threads.html>

- **Ch 16.2** — "Transfer Data Between Threads with Message Passing": `mpsc::channel`, `send()` takes ownership. *"Do not communicate by sharing memory; instead, share memory by communicating."*
  <https://doc.rust-lang.org/book/ch16-02-message-passing.html>

- **Ch 16.3** — "Shared-State Concurrency": `Mutex<T>`, `Arc<T>`, deadlocks. *"Rust can't protect you from all kinds of logic errors when you use `Mutex<T>`… `Mutex<T>` comes with the risk of creating deadlocks."* Note: lock poisoning and `PoisonError` are not discussed in this chapter — see the std library docs at <https://doc.rust-lang.org/std/sync/struct.Mutex.html#poisoning>.
  <https://doc.rust-lang.org/book/ch16-03-shared-state.html>

- **Ch 16.4** — "Extensible Concurrency with `Send` and `Sync`": Send transfers ownership across threads; Sync allows shared references across threads; `Rc<T>` is `!Send` because its reference count is not atomic; manual impl of Send/Sync is `unsafe`.
  <https://doc.rust-lang.org/book/ch16-04-extensible-concurrency-sync-and-send.html>

The async/tokio caveats (guard across `.await`, `spawn_blocking`) are runtime-level rules; the Book's Send/Sync chapter is the foundation; Tokio's documentation and the async-book extend it to the executor model.

---

## Verification Hooks

Run all detectors in one sweep. Every command is heuristic — false positives are expected; rustc/clippy is the real gate.

```bash
echo "=== F1: MutexGuard across .await ==="
grep -rn '\.lock()' src/ | grep -vE 'tokio::sync|async_mutex'
# heuristic — matches all .lock() calls; confirm each is inside an async fn with a later .await; rustc/clippy is the real gate

echo "=== F2: Rc/RefCell in spawned contexts ==="
grep -rnE 'Rc::new|Rc<' src/ | grep -vE '#\[cfg\(test|// @single-thread'
grep -rnE 'RefCell::new|RefCell<' src/ | grep -vE '#\[cfg\(test|// @single-thread'
# heuristic — flag only hits in files that also contain thread::spawn or tokio::spawn; rustc/clippy is the real gate

echo "=== F3: Multiple lock() calls in same file (deadlock risk) ==="
grep -rn '\.lock()' src/ | awk -F: '{print $1}' | sort | uniq -d
# heuristic — file-granular; manually check whether two locks are acquired in the same scope; rustc/clippy is the real gate

echo "=== F4: .lock().unwrap() without poison handling ==="
grep -rn '\.lock()\.unwrap()' src/ | grep -vE '#\[cfg\(test'
# heuristic — misses .lock() and .unwrap() on separate lines; rustc/clippy is the real gate

echo "=== F5: Blocking calls inside async without spawn_blocking ==="
grep -rnE 'thread::sleep|std::fs::|bcrypt::|argon2::' src/ \
  | grep -vE 'spawn_blocking|#\[cfg\(test'
# heuristic — confirm each match is inside an async fn body; rustc/clippy is the real gate

echo "=== F6: Cell/RefCell wrapped in Arc ==="
grep -rnE 'Arc<.*(Ref)?Cell<|Arc::new\((Cell|RefCell)::new' src/
# heuristic — review each to confirm no synchronization primitive wraps the inner type; rustc/clippy is the real gate

echo "=== F7: Arc<Mutex<T>> without write path (over-locking) ==="
grep -rnE 'Arc<Mutex<|Arc::new\(Mutex::new' src/
grep -rnE '\.lock\(\).*=|\.lock\(\).*push|\.lock\(\).*insert|\.lock\(\).*remove' src/
# heuristic — cross-reference both outputs; if no write pattern exists, consider Arc<T> or Arc<RwLock<T>>; rustc/clippy is the real gate
```

---

## Related Skills

- **rust-smart-pointers** — `Rc` vs `Arc` ownership semantics; when to prefer `Arc` even in single-threaded code for future-proofing.
- **rust-ownership-borrowing** — lifetime rules that govern what `move` closures can capture; why borrows cannot outlive the spawning scope.
- **leptos-hydration-discipline** — Forbidden 9 is the project-specific instance of Forbidden 1 and 2 here: `Rc`, `RefCell`, raw `PgConnection` across `.await` inside `#[server]` functions breaks the Tokio `Send` requirement and the SSR/WASM compilation boundary simultaneously.

---
> Source: [adelabdelgawad/rust-fullstack-agents](https://github.com/adelabdelgawad/rust-fullstack-agents) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
