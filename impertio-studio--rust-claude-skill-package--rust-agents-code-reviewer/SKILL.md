---
name: rust-agents-code-reviewer
description: > Use when this capability is needed.
metadata:
  author: Impertio-Studio
---

# Rust Agents Code Reviewer

This skill reviews already-written Rust code for quality, idiom, and latent bugs. It is the deep-review counterpart to [[rust-agents-orchestrator]] (which routes and runs a fast checklist) and [[rust-agents-compile-fix]] (which iterates on compiler errors).

ALWAYS use this skill when a prompt asks "review this Rust code", "is this idiomatic", "find code smells", or before approving a Rust pull request. NEVER treat compilation success as a passed review: `cargo build` proves type-checking, not quality.

This skill reviews. It does NOT route tasks (that is `rust-agents-orchestrator`) and does NOT fix compiler errors (that is `rust-agents-compile-fix`). Treating these three as interchangeable is an anti-pattern.

## The Review Loop

ALWAYS follow this order. Each step is a hard gate.

1. **Mechanical pass**: run `cargo clippy --all-targets --all-features -- -D warnings` and `cargo fmt --all --check`. Resolve every clippy `correctness` and `suspicious` finding before human review continues.
2. **Naming pass**: check every identifier against the Rust API Guidelines casing table below.
3. **Error-handling pass**: locate every `.unwrap()` / `.expect()` / `panic!` and demand a justification for each.
4. **Async pass**: check every `async fn` and spawned future for blocking calls, locks held across `.await`, and `Send` bounds.
5. **Memory pass**: check every `Arc<Mutex<T>>`, `RwLock`, `RefCell`, and `.clone()` for appropriateness.
6. **Suppression pass**: check every `#[allow(...)]` for a reason comment; recommend `#[expect(...)]`.
7. **Verdict**: produce the structured verdict from the review checklist in `references/methods.md`.

## Clippy Lint Categories

Verified against the Clippy lint reference. ALWAYS know a lint's category before acting on it: a `correctness` finding is a bug, a `pedantic` finding is a preference.

| Category | Default level | What it means | Reviewer action |
|----------|---------------|---------------|-----------------|
| `correctness` | **deny** | Code is objectively wrong, a real bug | BLOCK. Must be fixed. |
| `suspicious` | warn | Very likely a mistake | BLOCK unless author justifies. |
| `style` | warn | Non-idiomatic but functionally fine | Request change; not a blocker if justified. |
| `complexity` | warn | A simpler equivalent form exists | Request change; not a blocker if justified. |
| `perf` | warn | A faster equivalent form exists | Request change; weigh against readability. |
| `pedantic` | allow | Stricter, opt-in standard | Advisory only. NEVER a blanket blocker. |
| `nursery` | allow | Unstable, under development | Advisory only. May have false positives. |
| `restriction` | allow | Situational constraints | NEVER blanket-enable. Pick individual lints. |
| `cargo` | allow | `Cargo.toml` metadata lints | Advisory; enable per project. |

`clippy::all` = correctness + suspicious + style + complexity + perf. `pedantic`, `nursery`, `restriction`, `cargo` are explicit opt-in.

**Recommended CI baseline**: `deny` correctness and suspicious; `warn` style, complexity, and perf. Do NOT add `clippy::restriction` as a group: it contains mutually contradictory lints (for example `clippy::unwrap_used` versus `clippy::expect_used`, `clippy::single_char_lifetime_names` versus the API Guidelines). Enable individual restriction lints with intent, for example `clippy::unwrap_used` in library crates.

## Naming Idioms (Rust API Guidelines)

Verified against the Rust API Guidelines naming page (RFC 430).

| Item | Casing | Example |
|------|--------|---------|
| Crates, modules | `snake_case` | `my_crate`, `net_io` |
| Types, traits, enum variants | `UpperCamelCase` | `HttpClient`, `Display`, `Pending` |
| Functions, methods, local variables | `snake_case` | `parse_line`, `byte_count` |
| Constants, statics | `SCREAMING_SNAKE_CASE` | `MAX_RETRIES`, `GLOBAL_POOL` |
| Type parameters | single uppercase letter | `T`, `K`, `V` |
| Lifetimes | short lowercase | `'a`, `'src` |

Acronyms count as one word in `UpperCamelCase`: write `Uuid`, `HttpId`, NOT `UUID`, `HTTPId`.

**Conversion-method prefixes** (review every `fn` that returns a different type):

| Prefix | Direction | Cost | Example |
|--------|-----------|------|---------|
| `as_` | borrowed to borrowed view | free | `str::as_bytes` |
| `to_` | borrowed to owned, or `Copy` to `Copy` | expensive | `str::to_string` |
| `into_` | owned to owned, consuming `self` | variable | `String::into_bytes` |

**Getters**: NO `get_` prefix. Use the field name: `config.timeout()`, NOT `config.get_timeout()`. The single exception is one obvious value, like `Cell::get`.

**Iterator methods**: collections expose `iter()`, `iter_mut()`, `into_iter()` returning `Iter`, `IterMut`, `IntoIter`.

## Decision Tree: Reviewing `.unwrap()` and `.expect()`

```
A .unwrap() / .expect() / panic! is found
├── In #[cfg(test)] code or a tests/ file?
│   └── ACCEPT. Panicking is the test-failure mechanism.
├── In fn main() returning () or Result?
│   └── ACCEPT if the panic message is actionable; PREFER returning Result.
├── On a value provably infallible at this point?
│   ├── A // comment explains the invariant? --> ACCEPT.
│   └── No comment? --> REQUEST a justifying comment or .expect("reason").
└── On genuinely fallible input (parse, I/O, env, network)?
    └── BLOCK. Require `?`, a match, or a recoverable error path.
```

`.expect("...")` with a clear invariant message is always preferable to bare `.unwrap()`. Library code (a crate others depend on) should return `Result`, never panic on fallible paths.

## Decision Tree: Reviewing Async Code

```
Reviewing an async fn or a spawned future
├── std::thread::sleep / std::fs / blocking DB call in the body?
│   └── BLOCK. It stalls the runtime worker for every task.
│       Fix: tokio::time::sleep, tokio::fs, or wrap in spawn_blocking.
├── A std::sync::Mutex / RwLock guard held across an `.await`?
│   └── BLOCK. The guard is !Send; it can deadlock and may not compile.
│       Fix: drop the guard before `.await`, or use tokio::sync::Mutex.
├── CPU-bound loop (hashing, compression, large parse) in async fn?
│   └── BLOCK. Move it to spawn_blocking or rayon.
└── Future passed to tokio::spawn?
    └── It must be Send + 'static. Verify no Rc / RefCell / raw pointer
        is captured. If !Send is intended, use spawn_local on a LocalSet.
```

## Decision Tree: Reviewing Shared-State Memory Patterns

```
The code shares mutable state
├── State is a single integer / bool / pointer?
│   └── PREFER an atomic (AtomicUsize, AtomicBool). Arc<Mutex<i32>> is wasteful.
├── State is read far more often than written?
│   └── PREFER Arc<RwLock<T>> over Arc<Mutex<T>>.
├── State is a compound type written from multiple threads?
│   └── Arc<Mutex<T>> is appropriate.
├── RefCell or Rc used, and the type crosses a thread boundary?
│   └── BLOCK. RefCell and Rc are !Sync / !Send. Use Mutex / RwLock + Arc.
└── A `.clone()` on a String / Vec / Arc inside a hot path?
    └── REQUEST review: prefer borrowing, or move ownership instead.
```

`.clone()` on a `Copy` type (`u32`, `bool`) is always redundant: flag `clippy::clone_on_copy`. `.clone()` on an `Arc` is cheap (a refcount bump) and is fine when sharing ownership.

## Reviewing Lint Suppressions

Every `#[allow(some_lint)]` MUST carry a reason. ALWAYS request one of:

```rust
// allowed: this u128 cast is range-checked on the line above
#[allow(clippy::cast_possible_truncation)]
let n = checked_value as u32;
```

PREFER `#[expect(...)]` (stable since Rust 1.81) over `#[allow(...)]`. `#[expect]` itself warns (`unfulfilled_lint_expectations`) if the lint stops firing, so a stale suppression is caught when the underlying code is fixed:

```rust
#[expect(clippy::too_many_arguments, reason = "builder is split out in issue #214")]
fn assemble(...) { ... }
```

BLOCK a crate-level `#![allow(clippy::all)]` or `#![allow(warnings)]`: it silences `correctness` bugs alongside style noise.

## Quick Review Checklist

Run top to bottom. See `references/methods.md` for the full verdict template and commands.

1. `cargo clippy --all-targets --all-features -- -D warnings` is clean.
2. `cargo fmt --all --check` is clean.
3. Every identifier matches the API Guidelines casing table.
4. Conversion methods use the correct `as_` / `to_` / `into_` prefix.
5. Every `.unwrap()` / `.expect()` / `panic!` is justified per the decision tree.
6. `?` is used over manual `match` for error propagation; library errors are structured.
7. No blocking call and no held lock crosses an `.await`.
8. Spawned futures are `Send + 'static`.
9. Shared-state primitive choice (atomic / `Mutex` / `RwLock` / `RefCell`) fits the access pattern.
10. No needless `.clone()`; `.clone()` on `Copy` types removed.
11. Every `#[allow]` has a reason; `#[expect]` preferred; no crate-wide blanket allow.
12. `cargo test --all-targets` passes, including doc tests.

## References

- `references/methods.md`: clippy commands, lint-level configuration, the verdict template, CI baseline.
- `references/examples.md`: before/after review pairs for each pass.
- `references/anti-patterns.md`: reviewer mistakes, why they fail, the fix.

## Related Skills

- [[rust-agents-orchestrator]]: routes tasks and runs the fast cross-skill checklist.
- [[rust-agents-compile-fix]]: iterates on rustc compiler errors.
- [[rust-impl-error-handling]]: how to structure `Result`, `?`, `anyhow`, `thiserror`.
- [[rust-impl-async-tokio]]: correct `tokio::spawn`, `spawn_blocking`, `select!` usage.
- [[rust-impl-concurrency]]: `Arc`, `Mutex`, `RwLock`, atomics, channels.
- [[rust-core-toolchain]]: clippy, rustfmt, components, target triples.

## Sources

- Rust Clippy lint reference: https://rust-lang.github.io/rust-clippy/master/
- Rust API Guidelines, naming: https://rust-lang.github.io/api-guidelines/naming.html

---
> Source: [Impertio-Studio/Rust-Claude-Skill-Package](https://github.com/Impertio-Studio/Rust-Claude-Skill-Package) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
