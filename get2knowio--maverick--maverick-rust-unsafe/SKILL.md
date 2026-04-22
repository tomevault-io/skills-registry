---
name: maverick-rust-unsafe
description: Rust unsafe code, FFI, and safety invariants Use when this capability is needed.
metadata:
  author: get2knowio
---

# Rust Unsafe Skill

## When to Use Unsafe
- FFI with C libraries
- Raw pointers for performance
- Type transmutation (rare, dangerous)
- Unsafe trait implementations

## Safety Documentation
```rust
/// SAFETY: The slice is guaranteed to be non-empty by the caller.
/// This is enforced by the type system via NonEmptySlice.
unsafe fn get_first_unchecked(slice: &[i32]) -> i32 {
    debug_assert!(!slice.is_empty());
    *slice.get_unchecked(0)
}
```

## Avoid Unsafe When Possible
```rust
// BAD - unjustified unsafe
unsafe fn get_first(slice: &[i32]) -> i32 {
    *slice.get_unchecked(0)
}

// GOOD - safe alternative
fn get_first(slice: &[i32]) -> Option<&i32> {
    slice.first()
}
```

## Review Severity
- **CRITICAL**: Unsafe without justification, potential undefined behavior
- **MAJOR**: Missing safety documentation, unsafe could be safe
- **MINOR**: Debug assertions missing in unsafe code

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/get2knowio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
