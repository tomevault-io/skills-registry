---
name: line-count
description: Count lines of code using cloc with predefined scopes (source, tests, examples) Use when this capability is needed.
metadata:
  author: adamfilli
---

# Line Count

Count lines of Python code in the project using `cloc`. Three predefined scopes are available.

## Scopes

| Scope | Argument | Directories | Command |
|-------|----------|-------------|---------|
| **Source only** | `src` | `happysimulator/` | `cloc happysimulator/ --exclude-dir=__pycache__,.egg-info` |
| **Source + Tests** | `src+tests` | `happysimulator/`, `tests/` | `cloc happysimulator/ tests/ --exclude-dir=__pycache__,.egg-info` |
| **All** | `all` | `happysimulator/`, `tests/`, `examples/` | `cloc happysimulator/ tests/ examples/ --exclude-dir=__pycache__,.egg-info` |

## Instructions

1. If the user provides a scope argument (`src`, `src+tests`, or `all`), run that scope directly
2. If no argument is provided, ask the user which scope they want using these three options
3. Run the appropriate `cloc` command from the project root
4. Present the results in a summary table showing files, blank lines, comments, and code lines
5. If multiple directories are included, also run each directory individually so the user can see a per-directory breakdown
6. At the end, display a concise summary like:

```
Library (happysimulator/):  X,XXX lines
Tests (tests/):             X,XXX lines
Examples (examples/):       X,XXX lines
──────────────────────────────────────
Total:                      X,XXX lines
```

## Notes

- Requires `cloc` to be installed and available on PATH
- If `cloc` is not found, inform the user to install it (`choco install cloc`, `scoop install cloc`, or `pip install cloc`)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adamfilli) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
