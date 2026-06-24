---
name: rust
description: Rust specialist for async services, safe systems programming, and production tooling with Tokio, Axum, SQLx, and clippy-driven quality checks. Use when building or refactoring Rust code. Use when this capability is needed.
metadata:
  author: Ven0m0
---

# Rust Development (Lean)

## When to use

- `.rs` files and `Cargo.toml`
- Async services, CLI tools, systems components
- Ownership/lifetime, trait, or performance-sensitive work

## Defaults

- Prefer explicit error types at library boundaries.
- Keep unsafe usage isolated and documented.
- Use clippy and rustfmt as non-optional quality gates.
- Favor simple ownership flows over complex lifetimes when possible.

## Quick workflow

1. Identify crate boundaries and public APIs.
2. Implement minimal change with clear types/errors.
3. Add/update tests for changed behavior.
4. Run fmt, clippy, and tests.

## Commands

- Format: `cargo fmt --all`
- Lint: `cargo clippy --all-targets --all-features -- -D warnings`
- Test: `cargo test --all-features`
- Build release: `cargo build --release`

## Implementation checklist

- `Result<T, E>` and `?` used consistently.
- Avoid panics in non-test code paths unless explicitly fatal.
- Serialization contracts versioned when exposed externally.
- Concurrency primitives chosen deliberately (`Mutex`, `RwLock`, channels, semaphore).

## Validation checklist

- `cargo fmt` clean.
- `cargo clippy` clean.
- Tests pass.
- New dependencies justified.

## References

- [references/reference.md](./references/reference.md) - compact guidance and docs links
- [references/examples.md](./references/examples.md) - concise service/test patterns
- `../AGENT_SKILL_SPEC.md` - shared Anthropic/Copilot alignment

---
> Source: [Ven0m0/steelseriesgg-rs](https://github.com/Ven0m0/steelseriesgg-rs) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
