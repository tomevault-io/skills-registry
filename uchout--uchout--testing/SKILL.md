---
name: rust-testing
description: When and what to test in Rust. Types handle compile-time invariants; tests verify runtime behavior types cannot express. Use when this capability is needed.
metadata:
  author: uchout
---

# Rust Testing

Core principle: **Test what types cannot prove.**

## When Types Are Enough (Skip Tests)

Types already guarantee these - no tests needed:

| Guarantee | Type Mechanism |
|-----------|---------------|
| Can't mix UserId/OrderId | Newtype |
| Invalid state transitions | State machine (PhantomData) |
| Missing required fields | Type-state builder |
| Null/missing values | Option |

```rust
// ❌ Pointless test - compiler already rejects wrong types
#[test]
fn user_id_not_mixable_with_order_id() { }

// ❌ Pointless - method doesn't exist on Draft
#[test]
fn draft_order_cannot_pay() { }
```

## When Tests Are Necessary

Test runtime behavior types cannot express:

| Test | Reason |
|------|--------|
| Validation logic | Regex, range checks, business rules |
| Algorithm correctness | Math, sorting, transformation |
| State transition *conditions* | "Can submit only if items > 0" |
| External interactions | DB, network, filesystem |
| Error paths | Specific error variants returned |

## Meaningful Tests

### 1. Validation Logic

Types say "Email is valid", tests prove the validation is correct.

```rust
#[test]
fn email_rejects_missing_at() {
    assert!(Email::new("invalid").is_err());
}

#[test]
fn email_rejects_empty() {
    assert!(Email::new("").is_err());
}
```

### 2. Conditional Transitions

Types enforce *which* transitions exist; tests verify *when* they succeed.

```rust
#[test]
fn cannot_submit_empty_order() {
    let order = Order::<Draft>::new();
    assert!(order.submit().is_err());  // Business rule, not type rule
}
```

### 3. Algorithm Correctness

```rust
#[test]
fn order_total_sums_items() {
    let mut order = Order::new();
    order.add(Item::new(100));
    order.add(Item::new(50));
    assert_eq!(order.total(), 150);
}
```

### 4. Error Variants

```rust
#[test]
fn returns_not_found_for_missing_user() {
    let result = repo.get_user(UserId::new(999));
    assert!(matches!(result, Err(RepoError::NotFound { .. })));
}
```

## Doc Tests

Use for **public API examples** that also verify correctness.

```rust
/// ```
/// let email = Email::new("user@example.com")?;
/// # Ok::<(), EmailError>(())
/// ```
///
/// ```compile_fail
/// // Proves type safety - won't compile
/// get_order(order_id, user_id);  // Wrong argument order
/// ```
```

`compile_fail` documents type guarantees without runtime tests.

## Integration Tests

Use `tests/` for cross-module behavior and external dependencies.

```
tests/
├── api_integration.rs    # Full workflows
└── common/mod.rs         # Shared fixtures
```

## What NOT to Test

- Derived traits (`Debug`, `Clone`)
- Simple getters/setters
- `From`/`Into` implementations
- Anything the type system already enforces

## Checklist

- [ ] No tests for type-enforced invariants
- [ ] Validation logic tested with edge cases
- [ ] Conditional transitions tested
- [ ] `compile_fail` doc tests document type safety
- [ ] Integration tests for cross-module workflows

## See Also

- `rust-type-driven-development` - types that eliminate tests
- `rust-error-handling` - testing error variants

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/uchout) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
