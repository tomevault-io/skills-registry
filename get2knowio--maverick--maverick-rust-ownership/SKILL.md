---
name: maverick-rust-ownership
description: Rust ownership, borrowing, and lifetimes Use when this capability is needed.
metadata:
  author: get2knowio
---

# Rust Ownership Skill

## Ownership Rules
1. Each value has exactly one owner
2. When owner goes out of scope, value is dropped
3. Values can be moved or borrowed

## Borrowing Rules
- One mutable reference OR any number of immutable references
- References must always be valid

```rust
let s = String::from("hello");
let r1 = &s;      // OK
let r2 = &s;      // OK
let r3 = &mut s;  // ERROR: can't borrow as mutable
```

## Lifetimes
```rust
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() { x } else { y }
}
```

## Review Severity
- **CRITICAL**: Dangling references, use after move
- **MAJOR**: Unnecessary clones, incorrect lifetime annotations
- **MINOR**: Could use references instead of owned values

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/get2knowio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
