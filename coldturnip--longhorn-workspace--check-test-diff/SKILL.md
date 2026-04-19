---
name: check-test-diff
description: > Use when this capability is needed.
metadata:
  author: coldturnip
---

# Skill: check-test-diff

## Description

This skill enforces QA safety by guarding against accidental or excessive changes to Go test files (`*_test.go`) in git diffs. It fails if any test file is deleted, or if any test file has more than 100 lines deleted in a single diff.

## When to Use

- As a pre-merge or CI guard to prevent accidental removal or mass modification of test coverage.
- When reviewing large or automated changes that may unintentionally impact test files.

## Usage

Run the shell script in this directory to check the current diff:

```
bash .opencode/skills/check-test-diff/check_test_diff.sh
```

To check a specific repository (optional):

```
bash .opencode/skills/check-test-diff/check_test_diff.sh --repo repo/<repo-name>
```

## Expected Outcomes

- **Exit code 0**: No forbidden test file deletions or excessive line deletions detected. Prints a summary of test file changes.
- **Exit code 1**: One or more `*_test.go` files deleted, or a test file has >100 lines deleted. Prints details of violations and fails the check.

## Notes

- This skill does not run or select test suites; it only guards against risky test file diffs.
- ASCII-only output and file compliance enforced.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/coldturnip) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
