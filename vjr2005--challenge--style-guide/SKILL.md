---
name: style-guide
description: Code style and formatting rules. Use when writing code, naming types, or fixing SwiftLint issues. Use when this capability is needed.
metadata:
  author: vjr2005
---

# Skill: Style Guide

Read the official style guide documentation at `docs/StyleGuide.md` for complete formatting rules, naming conventions, and SwiftLint configuration.

## Quick Reference

| Rule | Convention |
|------|------------|
| Protocols | `Contract` suffix, always `any` when used as type |
| Mocks | `Mock` suffix (never prefix) |
| Mock variables | Also use `Mock` suffix |
| Parameters | `identifier` not `id` |
| Force unwrap | Never use `!` |
| Access modifiers | Never explicit `internal` |
| Pattern matching | `case let .foo(a, b):` not `case .foo(let a, let b):` |
| Line length | Max 140 characters |

## Checklist

- [ ] Line length ≤ 140 characters
- [ ] Imports alphabetized
- [ ] Protocols end with `Contract`
- [ ] Protocol types use `any` (e.g., `any TrackerContract`)
- [ ] Mocks end with `Mock` (suffix only, never prefix)
- [ ] Mock variables also use `Mock` suffix
- [ ] No force unwrap (`!`)
- [ ] Pattern matching: `case let` before the pattern, not `let` inside each binding
- [ ] No unused variables/parameters
- [ ] Code organized: properties → init → public methods → private extension

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vjr2005) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
