---
name: lint
description: Run golangci-lint on the project and fix issues. Use when checking code quality or before committing. Use when this capability is needed.
metadata:
  author: tuanldas
---

# Run Linter

## Steps

1. Run `golangci-lint run ./...` (or on `$ARGUMENTS` if a specific path is given)
2. If `--fix` is passed as argument, run with `--fix` flag
3. For each issue found:
   - Read the file and understand the context
   - Apply the fix using Edit tool
   - Explain what was changed and why
4. Re-run the linter to verify all issues are resolved

## Rules

- Follow existing code style in the project
- Do not add unnecessary comments or docstrings
- Do not refactor surrounding code - only fix linter issues

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tuanldas) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
