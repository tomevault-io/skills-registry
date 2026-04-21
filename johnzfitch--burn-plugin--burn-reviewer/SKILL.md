---
name: burn-reviewer
description: Reviews Burn deep learning code for idiomatic patterns, performance, backend portability, and best practices. Use when asked for code review, refactoring suggestions, or "is this idiomatic". Use when this capability is needed.
metadata:
  author: johnzfitch
---

# Burn Code Reviewer

Specialized skill for reviewing Burn code quality and best practices.

## Review Checklist

### 1. Idiomatic Patterns

**Module Definition**
- [ ] Uses `#[derive(Module)]` correctly
- [ ] Forward method signature is clean
- [ ] No manual parameter collection (Module handles it)

**Config Pattern**
- [ ] Separate Config struct with `#[derive(Config)]`
- [ ] Config has sensible defaults
- [ ] Model initialized via `config.init(&device)`

**Tensor Operations**
- [ ] Uses method chaining appropriately
- [ ] No unnecessary intermediate variables
- [ ] Correct use of in-place vs returning operations

### 2. Performance

**Unnecessary Clones**
- [ ] Clone only when tensor used multiple times
- [ ] No clone before last use
- [ ] Consider operation order to minimize clones

**Memory Efficiency**
- [ ] Large tensors not held longer than needed
- [ ] Batch processing used for large datasets
- [ ] Gradient tensors released after backward

**Backend Optimization**
- [ ] Operations fuseable where possible
- [ ] No excessive device transfers

### 3. Backend Portability

**Generic Code**
- [ ] Uses `B: Backend` generics, not concrete types
- [ ] Device handling is backend-agnostic
- [ ] No backend-specific code without feature gates

**Feature Flags**
- [ ] Correct features enabled in Cargo.toml
- [ ] No conflicting backend features
- [ ] Autodiff feature when training

### 4. Error Handling

**Graceful Failures**
- [ ] Shape assertions with clear messages
- [ ] Device availability checks
- [ ] File I/O error handling for weights

### 5. Testing

**Test Coverage**
- [ ] Forward pass tested
- [ ] Shape transformations verified
- [ ] Multiple backends tested (if portable)

## Review Output Format

```
BURN CODE REVIEW
================

File: src/model.rs

ISSUES
------
[severity] Line XX: Description
  Suggestion: How to fix
  Reference: Relevant doc chunk

GOOD PRACTICES
--------------
- Line XX: Good use of config pattern
- Line YY: Efficient tensor operation chain

SUMMARY
-------
Issues: X critical, Y warnings, Z suggestions
Overall: [Assessment]
```

## Severity Levels

- **Critical**: Will cause runtime errors or incorrect results
- **Warning**: Performance issue or non-idiomatic pattern
- **Suggestion**: Style improvement or minor optimization

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/johnzfitch) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
