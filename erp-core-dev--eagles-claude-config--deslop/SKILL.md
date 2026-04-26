---
name: deslop
description: Remove low-quality AI-generated code patterns Use when this capability is needed.
metadata:
  author: erp-core-dev
---

# Deslop -- Remove AI Slop

Detect and fix common low-quality AI-generated code patterns.

## Patterns Detected

1. **Unnecessary comments**: `// Get the user` before `GetUser()`
2. **Over-abstraction**: Single-use interfaces, unnecessary factories
3. **Verbose null checks**: Where null-conditional (`?.`) suffices
4. **Redundant try-catch**: Catching only to rethrow
5. **Magic strings**: Hardcoded values that should be constants
6. **Dead parameters**: Parameters that are never used
7. **Excessive logging**: Logging at every method entry/exit
8. **Copy-paste patterns**: Near-identical code blocks

## What To Do
1. Scan files for slop patterns using Grep
2. Generate report with file:line references
3. If --fix: Apply automated fixes where safe
4. If --report-only: Just output the report

## Arguments
- `--fix`: Auto-fix safe patterns
- `--report-only`: Report without changes

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/erp-core-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
