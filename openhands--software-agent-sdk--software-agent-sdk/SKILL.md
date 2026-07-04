---
name: python-linting
description: > Use when this capability is needed.
metadata:
  author: OpenHands
---

# Python Linting Skill

This skill provides instructions for linting Python code using ruff.

## How to Lint

Run ruff to check for issues:

```bash
ruff check .
```

To automatically fix issues:

```bash
ruff check --fix .
```

## Common Options

- `--select E,W` - Only check for errors and warnings
- `--ignore E501` - Ignore line length errors
- `--fix` - Automatically fix fixable issues

## Example Output

```
example.py:1:1: F401 [*] `os` imported but unused
example.py:5:5: E302 Expected 2 blank lines, found 1
Found 2 errors (1 fixable).
```

---
> Source: [OpenHands/software-agent-sdk](https://github.com/OpenHands/software-agent-sdk) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-07-04 -->
