---
name: code-review
description: Use when reviewing code changes, PRs, or validating that changes meet ICL quality standards before committing. Covers determinism checks, spec compliance, error handling, and test coverage.
metadata:
  author: icl-system
---

# Code Review

## When to Use

- Before committing any code changes
- Reviewing a PR or diff
- User asks to review code
- After implementing a feature, before marking it done

## Context

ICL code quality is critical — this runtime will be the canonical implementation that all bindings compile. Every public function is an API contract. Every bug ships to every language.

Reference files:
- `CONTRIBUTING.md` — code review checklist
- `CORE-SPECIFICATION.md` — does the code match the spec?
- `crates/icl-core/src/lib.rs` — public API surface

## Procedure

1. **Spec compliance** — Does the code match the relevant section of CORE-SPECIFICATION.md?
2. **Determinism** — Is there any source of non-determinism?
   - No `HashMap` (use `BTreeMap`)
   - No `rand` or randomness
   - No system time
   - No external I/O in core
   - No float operations without specified rounding
3. **Error handling** — All `Result<T>` with proper error types? No `unwrap()` in library code?
4. **Tests exist** — Unit test, error test, edge case test, determinism test (100 iterations)?
5. **Doc comments** — All public items documented with `///`? Includes `# Guarantees` section?
6. **Naming** — Follows conventions? (PascalCase types, snake_case functions, SCREAMING_CASE constants)
7. **Minimal surface** — Is anything `pub` that shouldn't be? Prefer `pub(crate)`.
8. **No TODO without issue** — Every `// TODO:` has an associated tracking issue
9. **Clippy clean** — `cargo clippy -- -D warnings` passes?
10. **Formatted** — `cargo fmt --check` passes?
11. **All tests pass** — `cargo test` succeeds?
12. **ROADMAP updated** — If this completes a roadmap item, is the checkbox checked?

## Determinism Red Flags

These patterns are ALWAYS wrong in `icl-core`:

```rust
// WRONG — non-deterministic iteration
use std::collections::HashMap;
let map: HashMap<String, Value> = ...;
for (k, v) in &map { ... }  // iteration order is random

// RIGHT — deterministic iteration
use std::collections::BTreeMap;
let map: BTreeMap<String, Value> = ...;
for (k, v) in &map { ... }  // iteration order is sorted

// WRONG — system time
let now = std::time::SystemTime::now();

// WRONG — randomness
let x = rand::random::<u32>();

// WRONG — panicking in library
let value = some_option.unwrap();

// RIGHT — error propagation
let value = some_option.ok_or(Error::MissingValue)?;
```

## Rules

- **All 12 checklist items must pass** before code is committed
- **Any determinism red flag = immediate rejection** — no exceptions
- **Tests are mandatory** — no test = no merge
- **Spec is authoritative** — if code contradicts spec, fix the code

## Anti-Patterns

- Approving code without running tests
- Skipping determinism review because "it's a small change"
- Allowing `unwrap()` in library code "just this once"
- Not checking spec compliance for parser/normalizer changes

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/icl-system) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
