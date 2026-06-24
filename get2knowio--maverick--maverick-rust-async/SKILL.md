---
name: maverick-rust-async
description: Rust async/await with Tokio and async patterns Use when this capability is needed.
metadata:
  author: get2knowio
---

# Rust Async Skill

## Tokio Basics
```rust
#[tokio::main]
async fn main() {
    let result = fetch_data().await;
}

async fn fetch_data() -> Result<String, Error> {
    // async operation
}
```

## Spawning Tasks
```rust
let handle = tokio::spawn(async move {
    expensive_operation().await
});

let result = handle.await?;
```

## Send + Sync Bounds
- `Send`: Can transfer across thread boundaries
- `Sync`: Can be shared across threads (&T is Send)
- Use `Arc` instead of `Rc` for thread-safe reference counting

## Review Severity
- **CRITICAL**: Blocking calls in async functions
- **MAJOR**: Using Rc in async code (not Send)
- **MINOR**: Not using async context managers

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/get2knowio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
