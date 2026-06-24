---
name: maverick-rust-testing
description: Rust testing patterns (unit, integration, property-based) Use when this capability is needed.
metadata:
  author: get2knowio
---

# Rust Testing Skill

## Unit Tests
```rust
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_add() {
        assert_eq!(add(2, 2), 4);
    }

    #[test]
    #[should_panic(expected = "overflow")]
    fn test_overflow() {
        add(i32::MAX, 1);
    }
}
```

## Integration Tests
Place in `tests/` directory (separate crate).

## Property-Based Testing
```rust
use proptest::prelude::*;

proptest! {
    #[test]
    fn test_reverse_involutive(ref vec in prop::collection::vec(any::<i32>(), 0..100)) {
        let reversed_twice: Vec<_> = vec.iter().rev().rev().cloned().collect();
        assert_eq!(vec, &reversed_twice);
    }
}
```

## Review Severity
- **CRITICAL**: No tests for new code
- **MAJOR**: Missing edge case tests
- **MINOR**: Missing documentation on tests

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/get2knowio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
