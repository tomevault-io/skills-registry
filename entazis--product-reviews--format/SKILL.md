---
name: format
description: Format and tidy the Go codebase safely (gofmt + goimports) while keeping diffs small. Use when the user asks to format code, fix import grouping, or prepare a clean patch before review/PR. Use when this capability is needed.
metadata:
  author: entazis
---

# Format

## Default workflow (small, safe diffs)

1. Run formatters:
   - `gofmt` for formatting
   - `goimports` for import grouping and unused imports
2. Avoid drive-by refactors. Formatting should not change behavior.
3. After formatting, run a quick test pass:
   - prefer targeted package tests first; run all tests if requested or if changes are broad

## Commands (examples)

Format everything:

```bash
gofmt -w .
```

If `goimports` is available:

```bash
goimports -w .
```

If you want to use the containerized toolchain (no local Go required), you can run tests in Docker afterward:

```bash
docker run --rm -v "$PWD":/src -w /src golang:1.25.6-alpine sh -c "go test ./..."
```

## Notes

- `gofmt` is non-negotiable for Go; keep code gofmt-clean.
- Import grouping should match Go conventions (std vs third-party).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/entazis) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
