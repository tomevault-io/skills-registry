---
name: rust
description: When writing, refactoring, testing, or configuring Rust — follow Bryan's toolchain, error-handling, crate, and structure preferences and the standard verification gate. Use when this capability is needed.
metadata:
  author: bryan824
---

# Rust

Write small, idiomatic, readable Rust for Bryan's projects — clear over clever.
Respect the repo's toolchain, and prove changes with the standard gate before
calling them done.

Resist: hiding errors with `unwrap`/`expect`/`panic`/`assert` on user-controlled input,
adding crates that don't cut real complexity, clever code, `git add .`/`-A`.

- Respect repo `rust-toolchain.toml`/`rust-toolchain` first; `rustup` for toolchains,
  `cargo` for tasks. Scan before edits: `rustup show active-toolchain`,
  `cargo metadata --no-deps --format-version 1`, `cargo check`; read `AGENTS.md`, architecture, README.
- Errors are typed and surfaced. Prefer `snafu` — libraries return `Result<T, Error>` with
  typed errors; apps use context selectors, print concise top-level errors, and exit non-zero.
  Avoid `anyhow`/`thiserror` unless the project already uses them; for zero-dep projects
  implement `std::error::Error` by hand.
- Add a crate only when it reduces meaningful complexity, and say why. Prefer `clap`
  (nontrivial CLI), `serde`/`serde_json`/`toml` (structured formats), `axum` (HTTP),
  `diesel` (relational + migrations), `tempfile` (dev). Avoid broad frameworks in small CLIs.
- Keep `src/main.rs` thin (parse → call lib → print); logic in `src/lib.rs`, modules by
  domain (parser, renderer, cli, error). Validate bounds before slicing; prefer `&str`/`Path`
  over `String`/`PathBuf`; use `*::from_le_bytes` for binary. Test malformed input and edge offsets.
- Verify before done: `cargo fmt -- --check`, `cargo clippy --all-targets -- -D warnings`,
  `cargo test`, `cargo check` (add `rustfmt`/`clippy` components if missing).

Deliver: the change, why any new crate earned its place, and the verification results.
Stage specific paths only, and commit with Conventional Commits when the work is complete.

---
> Source: [bryan824/kirin-pi](https://github.com/bryan824/kirin-pi) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
