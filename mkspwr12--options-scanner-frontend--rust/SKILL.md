---
name: rust
description: Build safe, concurrent, and performant systems with Rust. Use when writing Rust applications, implementing ownership and borrowing patterns, building concurrent code with threads/async, designing trait-based abstractions, or optimizing Rust performance. Use when this capability is needed.
metadata:
  author: mkspwr12
---

# Rust Development

> **Purpose**: Best practices for Rust development including ownership, error handling, concurrency, and safety patterns.

---

## When to Use This Skill

- Building Rust applications and systems
- Working with ownership, borrowing, and lifetimes
- Implementing error handling with Result and Option types
- Writing concurrent code with threads and async
- Designing trait-based abstractions

## Prerequisites

- Rust 1.75+ installed via rustup
- Cargo package manager

## Table of Contents

1. [Project Structure](#project-structure)
2. [Ownership and Borrowing](#ownership-and-borrowing)
3. [Error Handling](#error-handling)
4. [Traits and Generics](#traits-and-generics)
5. [Concurrency](#concurrency)
6. [Testing](#testing)
7. [Performance](#performance)
8. [Security](#security)
9. [Best Practices](#best-practices)

---

## Project Structure

### Standard Layout

```
project/
├── src/
│   ├── main.rs              # Binary entry point
│   ├── lib.rs               # Library root
│   ├── config.rs            # Configuration
│   ├── error.rs             # Error types
│   ├── models/
│   │   ├── mod.rs
│   │   └── user.rs
│   └── services/
│       ├── mod.rs
│       └── user_service.rs
├── tests/
│   └── integration_tests.rs
├── benches/
│   └── benchmarks.rs
├── examples/
│   └── basic_usage.rs
├── Cargo.toml
├── Cargo.lock
└── README.md
```

### Cargo.toml

```toml
[package]
name = "myapp"
version = "0.1.0"
edition = "2021"
authors = ["Your Name <you@example.com>"]
description = "A brief description"

[dependencies]
tokio = { version = "1", features = ["full"] }
serde = { version = "1", features = ["derive"] }
thiserror = "1"
anyhow = "1"

[dev-dependencies]
tokio-test = "0.4"

[profile.release]
lto = true
opt-level = 3
```

---

## Best Practices

### ✅ DO

- Use `clippy` for linting: `cargo clippy`
- Format with `rustfmt`: `cargo fmt`
- Prefer `&str` over `String` for function parameters
- Use `#[derive]` for common traits
- Write documentation with `///`
- Use `Result` for recoverable errors
- Leverage the type system for safety

### ❌ DON'T

- Use `unwrap()` in production code
- Ignore compiler warnings
- Use `unsafe` without clear justification
- Clone unnecessarily
- Write overly complex lifetimes
- Panic for expected error conditions

---

## References

- [The Rust Book](https://doc.rust-lang.org/book/)
- [Rust by Example](https://doc.rust-lang.org/rust-by-example/)
- [Rust API Guidelines](https://rust-lang.github.io/api-guidelines/)
- [Tokio Tutorial](https://tokio.rs/tokio/tutorial)

---

**Version**: 1.0
**Last Updated**: February 5, 2026


## Troubleshooting

| Issue | Solution |
|-------|----------|
| Borrow checker errors | Use .clone() for simple cases, refactor with owned types or Arc<Mutex<T>> |
| Lifetime annotation confusion | Start with owned types, add references only when performance requires it |
| Async runtime errors | Ensure using #[tokio::main] or equivalent, dont mix sync and async without spawn_blocking |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mkspwr12) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
