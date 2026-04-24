---
name: go-lint
description: Lint and format Go code via golangci-lint. Use when asked to lint code, fix lint errors, or check code quality. Use when this capability is needed.
metadata:
  author: mway
---

# Go Linter

**Reference:** `~/.config/agent/domain/coding/go/tooling.md` for Go linting details

## Instructions

Lint Go code using golangci-lint per AGENT.md guidelines:

### Standard Lint (with auto-fix)

```bash
golangci-lint run --new-false --fix ${ARGUMENTS}
```

Default to `./...` if no package path is provided.

### Lint Only (no fixes)

If user wants to see issues without applying fixes:
```bash
golangci-lint run --new-false ${ARGUMENTS}
```

### Lint Flags

- `--new-false`: Deprecated syntax for `--new=false` (show all issues)
- `--fix`: Automatically fix issues where possible
- Path defaults to `./...` (all packages)

### Common Usage

```bash
# Lint and fix all packages
golangci-lint run --new-false --fix ./...

# Lint specific package
golangci-lint run --new-false --fix ./path/to/package

# Check without fixing
golangci-lint run --new-false ./...
```

## Arguments

Target package(s): ${ARGUMENTS:-./...}

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mway) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
