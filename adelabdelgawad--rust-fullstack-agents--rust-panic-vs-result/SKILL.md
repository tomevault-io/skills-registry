---
name: rust-panic-vs-result
description: Language-level Rust error-handling philosophy (Ch 9). Covers panic! vs Result<T,E> vs Option, the ? operator, From-based error conversion, unwrap/expect discipline, Result in main, and the \"bad state vs expected failure\" decision rule from the Book. Keywords: panic, unwrap, expect, Result, Option, ? operator, error propagation, unreachable, todo, unimplemented, recoverable, unrecoverable, From conversion, propagate, panic!, panic macro, unwrap_or, unwrap_or_else, ok_or, ok_or_else, map_err, Box<dyn Error>, exit code. Auto-triggers on ANY `.unwrap()`/`.expect()` outside tests, any `panic!`/`unreachable!`/`todo!`/`unimplemented!` in non-test code, any fn returning Result, any `?` usage, any error-conversion question. For framework wiring (AppError, thiserror, IntoResponse, ServerFnError) see [[rust-error-handling]]. Use when this capability is needed.
metadata:
  author: adelabdelgawad
---

# Panic vs Result (Error-Handling Philosophy)

The Book (Ch 9) draws a sharp line: **`panic!` = unrecoverable bug**; **`Result<T,E>` = expected, recoverable failure**. Rust has no exceptions. The compiler enforces that every `Result` is handled, making error paths explicit and auditable at compile time.

> "Most languages don't distinguish between these two kinds of errors and handle both in the same way, using mechanisms such as exceptions. Rust doesn't have exceptions."
> — *The Rust Programming Language*, Ch 9 intro

> "When you choose to return a `Result` value, you give the calling code options."
> — *The Rust Programming Language*, Ch 9.3

## When to Use

Invoke this skill when any of the following appear:

- Any `.unwrap()` or `.expect()` call outside a `#[cfg(test)]` or `#[test]` context
- Any `panic!`, `unreachable!`, `todo!`, or `unimplemented!` in non-test production code
- Any function signature returning `Result<T, E>` or using the `?` operator
- Any question about when to panic vs. propagate an error
- Any `Option` in a service or repo return type where `None` represents a domain error

---

## Decision Table

| Situation | Correct mechanism |
|---|---|
| Impossible state that proves a bug exists | `panic!` / `unreachable!` (document the invariant) |
| Provable invariant — compiler can't see it | `.expect("invariant: …")` with an explanation string |
| Example, prototype, or `#[cfg(test)]` block | `.unwrap()` / `.expect()` acceptable |
| Expected failure — user input, I/O, network | `Result<T, E>` + `?` propagation |
| Absent value where absence is meaningful/normal | `Option<T>` |
| Absent value where absence is an error | `Result<T, E>` — not `Option` |
| Library / service / handler production path | `Result<T, E>` — never `unwrap()` |
| Contract violation by a caller (bad args to lib fn) | `panic!` — it is the caller's bug |
| Security risk from invalid values | `panic!` — fail loudly rather than proceed unsafely |
| Startup / config loading in `main.rs` | `Result<(), E>` from `main`, or `anyhow` glue |

---

## Core Idioms

### `?` over explicit match-and-return

```rust
// ✅ The ? operator propagates Err after running From::from on it
fn load_config(path: &str) -> Result<Config, AppError> {
    let text = std::fs::read_to_string(path)?;   // io::Error → AppError via From
    let cfg: Config = toml::from_str(&text)?;    // toml::Error → AppError via From
    Ok(cfg)
}

// ❌ Verbose hand-rolled propagation — identical semantics, 4× the noise
fn load_config(path: &str) -> Result<Config, AppError> {
    let text = match std::fs::read_to_string(path) {
        Ok(t) => t,
        Err(e) => return Err(e.into()),
    };
    // …
}
```

### `unwrap_or` / `unwrap_or_else` / `ok_or` for inline recovery

```rust
// ✅ Provide a fallback — no panic path
let port: u16 = env::var("PORT")
    .unwrap_or_else(|_| "8080".to_string())
    .parse()
    .unwrap_or(8080);

// ✅ Convert Option to Result when absence is an error
let user = cache.get(&id).ok_or(AppError::NotFound)?;

// ❌ .unwrap() on a fallible env read in a handler — panics if var is missing
let port: u16 = env::var("PORT").unwrap().parse().unwrap();
```

### `expect("invariant: …")` with a documented invariant

```rust
// ✅ Compiler can't prove this, but we can — document why it can never fail
let home: IpAddr = "127.0.0.1"
    .parse()
    .expect("invariant: 127.0.0.1 is a valid IpAddr literal");

// ❌ expect() with a vague symptom description — gives no guidance on the invariant
let cfg = CONFIG.get().expect("not initialized yet");
//                              ^^^ tells you what went wrong, not why it shouldn't
```

### `Result` from `main` for startup errors

```rust
// ✅ Exit code 1 on Err; integrates with ? at the top level
fn main() -> Result<(), Box<dyn std::error::Error>> {
    let cfg = Config::from_env()?;
    run(cfg)?;
    Ok(())
}

// ❌ .unwrap() in main — panic message is less useful than a typed error chain
fn main() {
    let cfg = Config::from_env().unwrap();
    run(cfg).unwrap();
}
```

---

## Forbidden Patterns

### Forbidden 1 — `.unwrap()` in library / service / handler production paths

**Forbidden:**
```rust
// In a service, handler, or any non-test production fn:
let conn = pool.get().unwrap();
let val: Foo = serde_json::from_str(&body).unwrap();
```

**Why (Book Ch 9.3):** "When you choose to return a `Result` value, you give the calling code options." Calling `.unwrap()` in a shared path makes the decision to abort on behalf of every caller, removes recovery options, and produces an opaque thread-panicked message instead of a typed error.

**Fix:**
```rust
// ✅
let conn = pool.get()?;
let val: Foo = serde_json::from_str(&body)?;
```

```bash
# Detector — .unwrap() outside tests (excludes .unwrap_or / .unwrap_or_else / .unwrap_or_default)
grep -rn '\.unwrap()' src/ \
  | grep -v '#\[cfg(test\|mod tests\|// @allow-unwrap' \
  | grep -v '\.unwrap_or'
```

---

### Forbidden 2 — `.expect()` without a documented invariant

**Forbidden:**
```rust
let val = map.get("key").expect("key should be there");
//                               ^^^ states desire, not invariant
```

**Why (Book Ch 9.2):** "In production-quality code, most Rustaceans choose `expect` rather than `unwrap` and give more context about why the operation is expected to always succeed." An `expect` with a vague message is marginally better than `.unwrap()` for debugging but does nothing to document the invariant — the reason the code should never reach the `Err`/`None` branch.

**Fix:** Either eliminate the `expect` with `?`, or document the provable invariant:
```rust
// ✅ invariant documented — compiler can't see it, but the reader can
let val = map.get("key")
    .expect("invariant: key is inserted unconditionally in the constructor");
```

```bash
# Detector — expect() without "invariant:" prefix
grep -rn '\.expect(' src/ \
  | grep -v 'invariant:\|#\[cfg(test\|mod tests\|// @allow-unwrap'
```

---

### Forbidden 3 — `panic!` / `unreachable!` / `todo!` / `unimplemented!` in non-test production code

**Forbidden:**
```rust
fn handle_event(e: Event) {
    match e {
        Event::Start => start(),
        _ => unreachable!("only Start is sent here"),  // runtime bomb
    }
}

fn coming_soon() -> Foo {
    todo!()   // in a deployed handler
}
```

**Why:** These macros unconditionally abort the thread. `todo!` and `unimplemented!` expand to `panic!` with a hardcoded message at runtime — they are prelude macros documented at [std::todo!](https://doc.rust-lang.org/std/macro.todo.html) and [std::unimplemented!](https://doc.rust-lang.org/std/macro.unimplemented.html); there is no Book ch09 citation that describes them. `unreachable!` is sometimes defensible at a provable exhaustion point, but if the match arms are truly exhaustive the compiler already enforces it — the macro adds a runtime abort where the compiler would give a compile error.

**Fix:** Return `Result` with a typed error, or if the branch is truly unreachable by construction, use a compile-time exhaustiveness check instead:
```rust
// ✅ typed error instead of panic
Event::Unknown(tag) => Err(AppError::UnknownEvent(tag))

// ✅ for genuinely future code: stub returns a typed error, not a panic
fn coming_soon() -> Result<Foo, AppError> {
    Err(AppError::NotImplemented)
}
```

```bash
# Detector — panic!/unreachable!/todo!/unimplemented! outside test modules
grep -rn 'panic!\|unreachable!\|todo!\|unimplemented!' src/ \
  | grep -v '#\[cfg(test\|mod tests\|// @allow-panic\|#\[test\]\|tests/'
```

---

### Forbidden 4 — Manual match-propagation instead of `?`

**Forbidden:**
```rust
let user = match repo.find(id).await {
    Ok(u) => u,
    Err(e) => return Err(e.into()),
};
```

**Why (Book Ch 9.2):** "The `?` placed after a `Result` value is defined to work in almost the same way as the `match` expressions." The manual pattern is not wrong — it is exactly what `?` expands to — but writing it by hand adds noise that hides intent and is one of the named anti-patterns in this codebase.

**Fix:**
```rust
// ✅
let user = repo.find(id).await?;
```

```bash
# Detector — Err(e) => return Err(e.into()) / Err(e) => return Err(From::from(e))
grep -rn 'return Err(.*\.into())' src/ | grep -v '//'
grep -rn 'return Err(From::from' src/ | grep -v '//'
```

---

### Forbidden 5 — Swallowing errors silently

**Forbidden:**
```rust
let _ = audit_log.write(entry);       // error discarded entirely
some_result.ok();                      // silently converts Err to None
channel.send(msg).ok();               // send failure hidden
```

**Why (Book Ch 9):** Rust requires you to acknowledge every `Result`. Using `let _ = …` or `.ok()` on a result that represents a meaningful failure deliberately circumvents this guarantee. Silent discard is almost always a bug: the caller has no way to know the operation failed, and downstream code may proceed on stale or corrupt state.

**Fix:** Log and/or propagate:
```rust
// ✅ propagate
audit_log.write(entry)?;

// ✅ log and continue when the error is genuinely non-fatal
if let Err(e) = audit_log.write(entry) {
    tracing::warn!(error = %e, "audit write failed — non-fatal");
}
```

```bash
# Detector — let _ = on a Result-returning call
grep -rn 'let _ =' src/ | grep -v 'test\|// @allow-discard'
# Heuristic detector — high false-positive rate: .ok() is idiomatic for optional env vars,
# optional lookups, and intentional Result→Option conversions. Review each hit manually
# before treating as a violation.
grep -rn '\.ok()' src/ | grep -v 'test\|// @allow-ok-discard\|Option'
```

---

### Forbidden 6 — `.unwrap()` on `.lock()` ignoring poisoning semantics

**Forbidden:**
```rust
let guard = mutex.lock().unwrap();
```

**Why:** A `Mutex::lock` returns `Err(PoisonError)` only when another thread panicked while holding the lock, leaving the protected data in an unknown state. Calling `.unwrap()` here re-panics the current thread, cascading the failure. In service code this can bring down the entire worker thread pool.

**Fix:** Decide consciously:
```rust
// ✅ recover the guard — accept potentially inconsistent data only if you audit it
let guard = mutex.lock().unwrap_or_else(|e| e.into_inner());

// ✅ propagate as a typed error — caller decides
let guard = mutex.lock().map_err(|_| AppError::LockPoisoned)?;
```

```bash
# Detector — .lock().unwrap() chains
grep -rn '\.lock()\.unwrap()' src/ | grep -v 'test\|// @allow-unwrap'
```

---

### Forbidden 7 — `Option` where absence is an error

**Forbidden:**
```rust
pub fn find_active_campaign(id: Uuid) -> Option<Campaign> { … }
// caller is forced to .unwrap() or write ad-hoc "should not be None" logic
```

**Why (Book Ch 9.2):** `Option` communicates "absence is a normal, expected outcome." When absence is a domain error — e.g., a referenced entity that must exist — use `Result` so the error type carries context and the `?` operator can propagate it cleanly. Returning `Option` where `None` means an error forces every caller to invent their own error wrapping.

**Fix:**
```rust
// ✅ absence is an error — callers get a typed error, can use ?
async fn find_active_campaign(id: Uuid) -> Result<Campaign, AppError> {
    repo.get(id).await?.ok_or(AppError::NotFound)
}
```

```bash
# Heuristic detector — pub fn returning Option in service/repo layers
grep -rn 'pub.*fn.*-> Option<' src/ \
  | grep -v 'test\|// @option-ok'
```

---

## Book References

- [Ch 9 — Error Handling](https://doc.rust-lang.org/book/ch09-00-error-handling.html) — philosophy overview; recoverable vs unrecoverable distinction; Rust has no exceptions.
- [Ch 9.1 — Unrecoverable Errors with `panic!`](https://doc.rust-lang.org/book/ch09-01-unrecoverable-errors-with-panic.html) — `panic!` macro; unwind vs abort; `RUST_BACKTRACE`.
- [Ch 9.2 — Recoverable Errors with `Result`](https://doc.rust-lang.org/book/ch09-02-recoverable-errors-with-result.html) — `Result<T,E>`; `?` operator; `From` trait conversion; `unwrap`/`expect`; `Result` from `main`.
- [Ch 9.3 — To `panic!` or Not to `panic!`](https://doc.rust-lang.org/book/ch09-03-to-panic-or-not-to-panic.html) — decision framework; examples/prototypes/tests; bad state vs expected failure; library vs application code; custom validation types.

---

## Related Skills

- **[[rust-error-handling]]** — Framework wiring: `AppError`, `thiserror`, `anyhow`, `IntoResponse`, `FromServerFnError`, `#[from]`/`#[source]`, handler patterns. Start here for project-level error type design; the present skill covers the language idioms underneath.
- **[[leptos-error-handling]]** — `<ErrorBoundary>`, server fn error propagation to the Leptos UI, `ServerFnError` display.
- **[[rust-pattern-matching]]** — Exhaustive match, `if let`, `while let`, guards — the foundation for manual `Result`/`Option` destructuring when `?` is not applicable.

---
> Source: [adelabdelgawad/rust-fullstack-agents](https://github.com/adelabdelgawad/rust-fullstack-agents) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
