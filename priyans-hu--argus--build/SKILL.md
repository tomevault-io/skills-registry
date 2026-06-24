---
name: build
description: Build the project. Use when compiling code, creating artifacts, or preparing for deployment. Use when this capability is needed.
metadata:
  author: priyans-hu
---

# Build - argus

Build the project.

## Command

```bash
go build ./...
```

## Description

Build all packages

## Entry Point

Main: `cmd/argus/main.go`

## On Failure

- Check for syntax errors in the output
- Verify all dependencies are installed
- Check for type errors (if applicable)
- Review recent changes that might have broken the build

## Success Criteria

- Build completes without errors
- Output artifacts are generated correctly

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/priyans-hu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
