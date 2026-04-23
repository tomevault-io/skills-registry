---
name: rust-edition-2024-to-2021-downgrade
description: | Use when this capability is needed.
metadata:
  author: strataga
---

# Rust Edition 2024 to 2021 Downgrade

## Problem
When downgrading a Rust project from edition 2024 to 2021, compilation fails with
"let chains are only allowed in Rust 2024 or later" errors. Let chains are a Rust
2024 feature that allows combining multiple `let` patterns with `&&` in if expressions.

## Context / Trigger Conditions
- Error message: `error: let chains are only allowed in Rust 2024 or later`
- Changing `edition = "2024"` to `edition = "2021"` in Cargo.toml
- Code contains patterns like `if let Some(x) = foo() && let Some(y) = bar() { ... }`
- Multiple files affected (let chains may be used extensively throughout codebase)

## Solution

### Step 1: Identify all let chains
```bash
grep -r "&& let " --include="*.rs" .
```

### Step 2: Refactor patterns

**Pattern A: Sequential let bindings**
```rust
// Before (2024)
if let Some(x) = foo()
    && let Some(y) = bar()
{
    // body
}

// After (2021)
if let Some(x) = foo() {
    if let Some(y) = bar() {
        // body
    }
}
```

**Pattern B: Let binding with boolean condition**
```rust
// Before (2024)
if let Some(x) = foo()
    && x.is_valid()
{
    // body
}

// After (2021)
if let Some(x) = foo() {
    if x.is_valid() {
        // body
    }
}
```

**Pattern C: Two independent Option/Result checks (can use tuple)**
```rust
// Before (2024)
if let Some(start) = text.find('{')
    && let Some(end) = text.rfind('}')
{
    // body using start and end
}

// After (2021) - tuple pattern when both needed
if let (Some(start), Some(end)) = (text.find('{'), text.rfind('}')) {
    // body using start and end
}
```

**Pattern D: Early return with nested conditions**
```rust
// Before (2024)
if let Some(parent) = path.parent()
    && !parent.exists()
{
    create_dir_all(parent)?;
}

// After (2021)
if let Some(parent) = path.parent() {
    if !parent.exists() {
        create_dir_all(parent)?;
    }
}
```

### Step 3: Handle test code
Test code often uses let chains liberally. Same patterns apply, but consider
using `let-else` for cleaner assertions:

```rust
// Before (2024)
match result {
    RecoveryResult::Recovered(info) => {
        assert!(info.is_valid());
    }
    _ => panic!("Expected Recovered"),
}

// After (2021) - let-else pattern (available in 2021)
let RecoveryResult::Recovered(info) = result else {
    panic!("Expected RecoveryResult::Recovered, got {:?}", result);
};
assert!(info.is_valid());
```

## Verification
1. Run `cargo build --workspace` - should complete without let chain errors
2. Run `cargo test --workspace` - all tests should pass
3. Run `cargo clippy --workspace` - may suggest collapsible_if (ignore if it suggests let chains)

## Example

Real-world refactoring from a 26-file change:

```rust
// Original (Rust 2024)
if let Ok(mut file_guard) = self.file.lock()
    && let Some(ref mut file) = *file_guard
        && let Ok(json) = serde_json::to_string(&event) {
            writeln!(file, "{}", json);
        }

// Refactored (Rust 2021)
if let Ok(mut file_guard) = self.file.lock() {
    if let Some(ref mut file) = *file_guard {
        if let Ok(json) = serde_json::to_string(&event) {
            writeln!(file, "{}", json);
        }
    }
}
```

## Notes
- Let chains were stabilized in Rust 2024; no way to enable them in 2021
- Clippy may suggest collapsible_if patterns that would reintroduce let chains - ignore these
- The tuple pattern (Pattern C) is more concise when both values are independent
- Some nesting depth increase is unavoidable; consider extracting to helper functions if too deep
- `let-else` IS available in Rust 2021 (stabilized in 1.65) - use it for cleaner error handling

## References
- [Rust 2024 Edition Guide - Let Chains](https://doc.rust-lang.org/edition-guide/rust-2024/index.html)
- [RFC 2497 - if/while let chains](https://rust-lang.github.io/rfcs/2497-if-let-chains.html)
- [let-else RFC 3137](https://rust-lang.github.io/rfcs/3137-let-else.html)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/strataga) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
