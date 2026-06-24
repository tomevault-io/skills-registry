---
name: rust-linter-configuration
description: >- Use when this capability is needed.
metadata:
  author: antonygiomarxdev
---

# Rust — linter configuration (Maverick)

## Files and commands

| Item | Location / command |
|------|---------------------|
| rustfmt | [`rustfmt.toml`](rustfmt.toml) |
| Workspace lint baseline | Root [`Cargo.toml`](Cargo.toml) `[workspace.lints.rust]` / `[workspace.lints.clippy]`; each crate `[lints] workspace = true` |
| Cargo aliases | [`.cargo/config.toml`](.cargo/config.toml) — `cargo fmt-check`, `cargo lint` |
| CI | [`.github/workflows/ci.yml`](.github/workflows/ci.yml) — `cargo fmt --all --check`, `cargo clippy --all-targets --all-features -- -D warnings` |

## Workspace baseline (declared in Cargo)

Rust has no single “Airbnb ESLint” package; common practice is **rustfmt + `clippy -D warnings` + a short `[workspace.lints]` list**. Maverick currently sets:

| Lint | Level | Role |
|------|--------|------|
| `rust::unused_must_use` | deny | Ignored `Result` / `MustUse` types fail CI |
| `rust::unsafe_op_in_unsafe_fn` | warn | Stricter `unsafe` blocks (becomes error under `-D warnings`) |
| `clippy::dbg_macro` | deny | No `dbg!` in shipped code |
| `clippy::todo` | warn | `todo!` fails CI under `-D warnings` |
| `clippy::unimplemented` | warn | `unimplemented!` fails CI under `-D warnings` |

New workspace members must include `[lints] workspace = true` in their `Cargo.toml`.

## Policy

- Treat **warnings as errors** in CI for Clippy (`-D warnings`). Local `cargo lint` should match.
- **Do not** commit unformatted code; `rustfmt` settings are shared—do not fight them in review.
- For `#[allow(clippy::...)]` or rustc allows: **justify in a comment**, smallest scope, prefer `#[expect(...)]` on nightly-only codebases (this repo uses stable—use `allow` with comment until MSRV supports `expect` if adopted).

## Optional extensions

- Add **`clippy.toml`** at the repo root when tuning thresholds or setting **`msrv`** once the workspace pins MSRV. See [Clippy configuration](https://doc.rust-lang.org/clippy/configuration.html).
- Enable extra lint **groups** (e.g. `clippy::pedantic`) only with team buy-in—usually too noisy for a blanket deny.

## Checklist

- [ ] Does a proposed `allow` have a one-line reason and minimal scope?
- [ ] Do local commands match CI (`fmt-check`, `lint`)?

---
> Source: [antonygiomarxdev/maverick](https://github.com/antonygiomarxdev/maverick) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
