---
name: rust-env-set-var-once
description: Sound env mutation in Rust 2024 requires BOTH std::sync::Once AND single-threaded invocation. One without the other is unsound. Use when this capability is needed.
metadata:
  author: drdave-flexnetos
---

# Rust env::set_var soundness — dual invariant

## Triggered by

- `unsafe { std::env::set_var(...) }` in any Rust file
- `LOAD_ONCE` / `call_once` patterns around env mutation
- env variable race conditions
- SAFETY comments about env mutation

## The dual invariant

In Rust 2024, `std::env::set_var` is `unsafe` because the process environment block is not thread-safe. Sound usage requires BOTH:

1. **`Once::call_once`** — guarantees the call site runs at most once per process
2. **Single-threaded invocation** — called at the top of `main()` BEFORE any thread spawns (no tokio runtime, no tracing flush thread, no signal handler)

Both invariants together — and only both — make `unsafe { set_var() }` defensible.

## The SAFETY comment must reference both

```rust
// SAFETY: Two invariants make this sound:
// 1. Once::call_once — body executes at most once per process.
// 2. Called from main() before tokio::main or any thread spawn —
//    no concurrent reader/writer of the environment block.
LOAD_ONCE.call_once(|| {
    unsafe { std::env::set_var("KEY", "VALUE"); }
});
```

## What NOT to do

- Do NOT wrap in `Once` alone and call it sound (another thread may read concurrently)
- Do NOT set env vars after spawning the tokio runtime or any threads
- Do NOT use `Once` without the `unsafe` block (the compiler requires it in edition 2024)

## Reference

Full pattern documentation: `.omc/skills/rust-env-set-var-once-plus-thread-spawn.md`
Code location: `aitop/aitop-doctor/src/env_loader.rs`

---
> Source: [drdave-flexnetos/ai-top-utility](https://github.com/drdave-flexnetos/ai-top-utility) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
