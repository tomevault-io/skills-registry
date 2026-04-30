---
name: running-tests
description: > Use when this capability is needed.
metadata:
  author: aiskillstore
---

# Test Command Detection

1. Check CLAUDE.md for project-specific test command
2. Auto-detect if not specified:

| File | Command |
|------|---------|
| `package.json` | `npm test` |
| `Cargo.toml` | `cargo test` |
| `justfile` | `just test` |
| `Makefile` | `make test` |
| `pyproject.toml` | `pytest` |
| `go.mod` | `go test ./...` |

3. Ask user if not found

# Test Execution

Run the detected command and report:
- Pass/Fail status
- Failed test names (if any)
- Error messages (if any)

# Failure Handling

1. Analyze failure cause
2. Determine root cause:

| Cause | Action |
|-------|--------|
| Implementation bug | Fix and commit |
| Test bug | Fix test and commit |
| Environment issue | Report to manager |

3. Re-run tests after fix
4. Confirm all tests pass

# Completion Report

- Test result (pass/fail)
- Test count
- Fixes applied (if any)
- Additional commits (if any)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aiskillstore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
