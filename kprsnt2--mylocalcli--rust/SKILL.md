---
name: rust
description: Best practices for Rust development including ownership, error handling, and async patterns. Use when this capability is needed.
metadata:
  author: kprsnt2
---

# Rust Best Practices

## Ownership & Borrowing
- Prefer borrowing over ownership transfer
- Use &str for function parameters over String
- Clone only when necessary
- Use Cow<str> for maybe-owned strings

## Error Handling
- Use Result<T, E> for recoverable errors
- Use panic! only for unrecoverable errors
- Use ? operator for error propagation
- Create custom error types with thiserror
- Use anyhow for application errors

## Idioms
- Use iterators over manual loops
- Prefer pattern matching with match
- Use Option for optional values
- Use derive macros for common traits
- Use clippy for linting

## Performance
- Use release builds for benchmarks
- Profile before optimizing
- Consider using Box for large stack types
- Use Arc/Mutex for shared state

## Async
- Use tokio or async-std runtime
- Use async/await for async code
- Avoid blocking in async context
- Use channels for async communication

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kprsnt2) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
