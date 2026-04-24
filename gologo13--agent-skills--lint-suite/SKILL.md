---
name: lint-suite
description: Run project linters, apply fixes, and ensure the codebase meets formatting and style require...; keywords: lint, pr, refactor, commit. Use only on explicit request; skip in routine implementation discussions. Use when this capability is needed.
metadata:
  author: gologo13
---

# Fix Lint Issues

## Overview

Run project linters, apply fixes, and ensure the codebase meets formatting and
style requirements before merging changes.

## Steps

1. **Execute linters**
    - Run the standard lint command with autofix enabled if available
    - Capture any remaining errors or warnings from the output
    - Identify files requiring manual attention
2. **Resolve findings**
    - Apply targeted fixes, keeping edits minimal and idiomatic
    - Refactor repeated issues such as unused variables or long functions
    - Update configuration or suppressions only when justified
3. **Verify cleanliness**
    - Re-run the lint command to ensure a zero-issue result
    - Spot-check key files for formatting and readability
    - Stage the changes with clear commit messages when satisfied

## Lint Checklist

- [ ] Linter executed with latest config
- [ ] Autofix results reviewed
- [ ] Manual issues resolved
- [ ] Final lint run passes cleanly
- [ ] Changes staged or ready for PR

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gologo13) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
