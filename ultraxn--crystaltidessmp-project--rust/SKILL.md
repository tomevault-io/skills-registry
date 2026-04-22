---
name: rust-async-performance
description: Rust development emphasizing async programming, performance optimization, and zero-cost abstractions. Use when this capability is needed.
metadata:
  author: ultraxn
---
# Rust (Async/Performance) Skill

Production Rust for systems, web (Axum), and async services. Zero allocations, optimal async, benchmark-driven.

## Core Principles
- **Fearless concurrency**: `tokio::spawn`, channels, async streams.
- **Zero-cost abstractions**: Benchmark everything. `criterion` mandatory.
- **Memory safety**: Never `unsafe` unless profiling proves necessary.

## Modular Behaviors
- **Project Setup**: `cargo new --bin`. `cargo add tokio --features=full`. `cargo fmt`, `cargo clippy --fix`.
- **Async Patterns**: `#[tokio::main]`. Use `tokio::select!`, `tokio::join!`. Streams over loops.
- **Web Services**: Axum v0.8+. `Router::new().route("/", get(handler))`. Extractors for auth/parsing.
- **Performance**: `cargo flamegraph`. Prefer `&str` over `String`. `smallvec`, `arrayvec` for small collections.
- **Error Handling**: `thiserror`, `anyhow`. `Result<T, Report>`. Context with `.context("failed")`.
- **Testing**: `#[cfg(test)] mod tests`. `tokio::test`. Property tests with `proptest`.
- **CLI**: `clap` v4+. `#[derive(Parser)]`. Subcommands pattern.
- **Optimization**: `cargo build --release`. `profile.release.lto = true`. Inline small functions.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ultraxn) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
