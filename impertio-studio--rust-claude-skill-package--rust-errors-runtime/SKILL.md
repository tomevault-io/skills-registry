---
name: rust-errors-runtime
description: > Use when this capability is needed.
metadata:
  author: Impertio-Studio
---

# Rust Runtime Panics

Diagnose and prevent runtime panics: index out of bounds, `.unwrap()` on
`None`/`Err`, integer overflow, division by zero. Pick the right panic strategy
(`unwind` vs `abort`), read backtraces, and guard FFI boundaries with
`catch_unwind`. A panic is for an unrecoverable bug; an expected failure is a
`Result`. Most runtime panics are programmer errors that a `Result` or a checked
method would have caught at the call site.

## Quick Reference

| Panic message | Root cause | Fix |
|----------------|-----------|-----|
| `index out of bounds: the len is N but the index is M` | `v[i]` with `i >= len` | Use `.get(i) -> Option`, validate `i` first |
| `called `Option::unwrap()` on a `None` value` | `.unwrap()` on `None` | `match` / `if let` / `?` / `.unwrap_or(...)` |
| `called `Result::unwrap()` on an `Err` value: ...` | `.unwrap()` on `Err` | Propagate with `?`, handle the `Err` arm |
| `attempt to add with overflow` | Integer overflow, debug build | `checked_add` / `saturating_add` / `wrapping_add` |
| `attempt to divide by zero` | Integer `/` or `%` with `0` divisor | Guard the divisor, use `checked_div` |
| `internal error: entered unreachable code` | `unreachable!()` hit | The invariant was wrong; fix the logic |
| `not yet implemented` / `not implemented` | `todo!()` / `unimplemented!()` | Implement the branch |

## ALWAYS / NEVER

- ALWAYS treat `.unwrap()` and `.expect()` in non-test, non-prototype code as a
  bug unless the `None`/`Err` case is provably impossible. Return `Result` and
  propagate with `?` instead.
- ALWAYS use `.get(i)` (returns `Option`) instead of `v[i]` when the index
  comes from user input, a file, a network message, or any external source.
- ALWAYS use a checked arithmetic method (`checked_add`, `saturating_add`,
  `wrapping_add`, `overflowing_add`) when an overflow is genuinely possible.
  Plain `+` is correct only when overflow is a true logic bug.
- ALWAYS set `RUST_BACKTRACE=1` before reproducing a panic. The top user frame
  in the backtrace is the panic site.
- ALWAYS wrap a Rust closure called from C in `std::panic::catch_unwind` when
  the panic strategy is `unwind`. An unwind crossing an FFI boundary into C is
  undefined behavior unless the function is `extern "C-unwind"`.
- ALWAYS use `.expect("clear invariant description")` over `.unwrap()` when a
  panic is genuinely intended. The message documents the assumption.
- NEVER assume a release build panics on integer overflow. With default
  profiles it WRAPS silently (`overflow-checks = false`). Only the dev profile
  panics.
- NEVER use `catch_unwind` as a general try/catch for recoverable errors. It is
  for FFI boundaries and thread isolation only; use `Result` for control flow.
- NEVER call `process::exit` from inside a function that owns values with `Drop`
  side effects you care about. `exit` runs `atexit` handlers but does NOT run
  destructors of local variables on the stack.
- NEVER `panic!` inside a `Drop` implementation. A panic during unwinding from
  another panic aborts the whole process immediately.

## Decision Tree: Should This Panic or Return Result

```
A failure can occur here.
|
+-- Is it an EXPECTED operational failure?
|   (file missing, network down, bad user input, parse failure)
|     -> Return Result<T, E>. The caller decides. Never panic.
|     -> See [[rust-impl-error-handling]] and [[rust-errors-thiserror-anyhow]].
|
+-- Is it a BUG (a violated invariant that should be impossible)?
|     -> panic! / unreachable! / .expect("why this is impossible")
|     -> A panic here means the program is in an undefined state.
|
+-- Is it a not-yet-written branch during development?
|     -> todo!() (will implement) or unimplemented!() (intentionally absent).
|
+-- Is it an index / arithmetic on external input?
      -> NOT a panic site. Use .get(), checked_*, validate, return Result.
```

## Panic Mechanics

A `panic!` starts unwinding (the default strategy). Unwinding walks the stack
frame by frame, running every `Drop` implementation, until it reaches the
thread boundary. The panicking thread terminates; if it is the main thread the
process exits with a non-zero code.

- `panic!("msg")` panics with a formatted message.
- `unreachable!()` panics: "this code path is logically impossible".
- `todo!()` panics: "implement me later".
- `unimplemented!()` panics: "this is intentionally not implemented".
- `assert!`, `assert_eq!`, `assert_ne!` panic when the condition fails. They
  run in ALL builds.
- `debug_assert!`, `debug_assert_eq!`, `debug_assert_ne!` panic only when
  `debug-assertions` is on (dev profile). They compile to nothing in release.

`Drop` during unwind: every local in scope is dropped as the stack unwinds. If
one of those `Drop` impls itself panics, there are now two active panics, and
the runtime aborts the process. This is why `Drop` must never panic.

## Unwind vs Abort

| Aspect | `panic = "unwind"` (default) | `panic = "abort"` |
|--------|------------------------------|-------------------|
| Stack unwinding | Yes, frame by frame | No |
| `Drop` runs on panic | Yes | No |
| `catch_unwind` can catch | Yes | No, process dies |
| Binary size | Larger (unwind tables) | Smaller |
| Panic speed | Slower | Faster |
| Thread isolation | A worker thread panic stays local | Whole process dies |

Set the strategy per profile in `Cargo.toml`:

```toml
[profile.release]
panic = "abort"
```

Tests, benchmarks, build scripts, and proc macros IGNORE the `panic` setting;
they always use `unwind` so the test harness can report failures.

Choose `abort` for: smallest binary, embedded / `no_std` targets, or when a
panic should never be survivable. Choose `unwind` (keep the default) when: you
run untrusted work on worker threads, you use `catch_unwind` at an FFI boundary,
or you rely on `Drop` cleanup during a panic.

## Backtraces

Rust prints a panic message but no stack trace by default. Enable it:

```bash
RUST_BACKTRACE=1 cargo run     # concise: user frames, trimmed
RUST_BACKTRACE=full cargo run  # verbose: every frame including std internals
```

Reading a backtrace: frames are listed innermost first. Skip the `core`/`std`
panic-machinery frames at the very top; the FIRST frame pointing at YOUR crate
is the panic site. A backtrace needs debug info; `strip = "symbols"` removes
symbol names, so keep `debug = "line-tables-only"` if you need backtraces in
release.

## Integer Overflow and Division

With debug-assertions on (dev profile), `a + b` PANICS on overflow with
`attempt to add with overflow`. With debug-assertions off (release profile,
default), the same `+` WRAPS in two's complement and produces a wrong value
silently. Force the panic in release by setting `overflow-checks = true` under
`[profile.release]`.

Pick the explicit method when overflow is possible (see `references/methods.md`):

- `checked_add` returns `Option<T>` (`None` on overflow). Use when overflow is
  an error to handle.
- `saturating_add` clamps to `T::MAX` / `T::MIN`. Use for counters and meters.
- `wrapping_add` wraps deliberately. Use for hashing, checksums, ring buffers.
- `overflowing_add` returns `(T, bool)` (result plus an overflowed flag).

Integer division and remainder (`/`, `%`) PANIC on a zero divisor in every
build with `attempt to divide by zero`. Use `checked_div` / `checked_rem`, or
guard the divisor. Floating point division by zero does NOT panic: it produces
`inf`, `-inf`, or `NaN`.

## catch_unwind for FFI

`std::panic::catch_unwind(closure)` runs the closure and returns
`Result<R, Box<dyn Any + Send>>`: `Ok(value)` on success, `Err(payload)` if the
closure panicked. It catches ONLY unwinding panics; with `panic = "abort"` a
panic still kills the process.

The closure must be `UnwindSafe`. When that bound is too strict and you have
verified the usage is sound, wrap it: `catch_unwind(AssertUnwindSafe(|| ...))`.

Use it at an FFI boundary so a Rust panic does not unwind into C:

```rust
use std::panic::{catch_unwind, AssertUnwindSafe};

#[unsafe(no_mangle)]
pub extern "C" fn process(ptr: *const u8, len: usize) -> i32 {
    let result = catch_unwind(AssertUnwindSafe(|| {
        // Rust work that might panic.
        do_work(ptr, len)
    }));
    match result {
        Ok(code) => code,
        Err(_) => -1, // Report failure to C; do NOT let the panic escape.
    }
}
```

`catch_unwind` is NOT general error handling. For expected failures return
`Result` and use `?`. See [[rust-impl-ffi-bindgen]] for the full FFI contract.

## process::abort vs process::exit

- `std::process::exit(code)` terminates the process with `code`. It runs C
  `atexit` handlers and flushes std streams, but does NOT run `Drop` of local
  variables still on the stack. Avoid calling it mid-function past values whose
  destructors matter.
- `std::process::abort()` terminates IMMEDIATELY via `SIGABRT`. No `Drop`, no
  `atexit`, no flushing. Use it only for an unrecoverable, must-die-now state.

To exit cleanly, return from `main` (optionally `-> ExitCode` or
`-> Result<(), E>`) so all destructors run, instead of calling `exit`.

## Reference Files

- `references/methods.md` : checked/saturating/wrapping/overflowing arithmetic,
  `.get` family, panic/assert macros, `catch_unwind`, `process` exits,
  profile knobs.
- `references/examples.md` : reproductions and fixes for each panic class,
  backtrace reading, an FFI `catch_unwind` guard, profile configuration.
- `references/anti-patterns.md` : the runtime-panic anti-patterns with the
  reason each fails and the deterministic fix.

## Cross-References

- [[rust-impl-error-handling]] : `Result`, `?`, `Option`, when to panic.
- [[rust-errors-thiserror-anyhow]] : structured and opaque error types.
- [[rust-impl-cargo-project]] : profiles, `Cargo.toml`, build configuration.
- [[rust-impl-ffi-bindgen]] : FFI boundary contract, `extern` functions.
- [[rust-agents-compile-fix]] : turning a panic into a compile-time guarantee.

---
> Source: [Impertio-Studio/Rust-Claude-Skill-Package](https://github.com/Impertio-Studio/Rust-Claude-Skill-Package) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
