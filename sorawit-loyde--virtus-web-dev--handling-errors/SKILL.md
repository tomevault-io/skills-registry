---
name: handling-errors
description: Provides patterns and strategies for error handling, debugging, and building resilient applications. Use this skill for troubleshooting production issues, designing error-resilient APIs, and implementing fault tolerance.
metadata:
  author: sorawit-loyde
---

# Error Handling & Troubleshooting Patterns

This skill provides a comprehensive guide to building resilient applications with robust error handling strategies. It covers core philosophies, language-specific implementation patterns, and universal distributed system patterns.

## When to Use This Skill
- **Implementing Error Handling**: When adding error logic to new features or APIs.
- **Debugging & Troubleshooting**: When diagnosing production issues or "unrecoverable" errors.
- **Reliability Engineering**: When implementing retries, circuit breakers, or graceful degradation.
- **Refactoring**: When improving error messages or exception hierarchies.

## Reference Documentation

### 1. Core Concepts & Best Practices
For high-level philosophies (Exceptions vs. Result types), error categories, and general do's and don'ts.
👉 **[`references/core-concepts.md`](references/core-concepts.md)**

### 2. Language-Specific Patterns
Concrete code examples and idiomatic patterns for Python, TypeScript/JavaScript, Rust, and Go.
👉 **[`references/language-patterns.md`](references/language-patterns.md)**

### 3. Universal & System Patterns
Architectural patterns like Circuit Breakers, Error Aggregation, and Graceful Degradation.
👉 **[`references/universal-patterns.md`](references/universal-patterns.md)**

## Quick Checklist
- [ ] **Fail Fast**: Validate input early.
- [ ] **Context**: Do error logs include timestamps, User IDs, and stack traces?
- [ ] **Cleanup**: Are resources (files, connections) closed in `finally` blocks?
- [ ] **User-Facing**: Are error messages helpful but secure (no internal secrets)?
- [ ] **Monitoring**: Are unexpected errors logged with high severity?

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sorawit-loyde) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
