---
name: refactor-efficiently
description: Efficient refactoring using the .only validation pattern — get one representative test passing before updating all tests. Use when a change will break multiple tests. Use when this capability is needed.
metadata:
  author: lumenize
---

# Refactor Efficiently

Safely refactor APIs, call signatures, and cross-cutting behavior by validating one test before updating all.

## Usage
`/refactor-efficiently <package-name>`

## Process
1. Mark one representative test as `.only`
2. Implement the new pattern in that test
3. Run `npm test` until it passes
4. Update remaining tests to match
5. Remove `.only` and run full suite

## Rules
- Never update all tests simultaneously
- Never leave `.only` in committed code
- Never create backward-compatible shims to avoid test updates

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lumenize) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
