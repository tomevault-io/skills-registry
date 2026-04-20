---
name: build-fixer
description: Systematic Python build error diagnosis. Use when pytest fails, ruff reports errors, basedpyright finds type issues, or uv has dependency problems. Use when this capability is needed.
metadata:
  author: businessplus-community
---

# Build Fixer - Python

Systematic workflow for diagnosing and fixing Python build errors.

## When to Use

- Tests are failing (`pytest` errors)
- Linting/formatting errors (`ruff` errors)
- Type checking errors (`basedpyright` errors)
- Dependency resolution issues (`uv` errors)

## Diagnostic Workflow

### Step 1: Identify the Tool

| Error Source | Indicators |
|--------------|------------|
| **pytest** | `FAILED`, `AssertionError`, `test_` in output |
| **ruff** | Error codes like `E501`, `F401`, `I001` |
| **basedpyright** | `error:`, `reportX` codes, type annotations |
| **uv** | `ResolutionFailure`, `PackageNotFound`, lock file errors |

### Step 2: Run Diagnostics

```bash
# Run all quality checks
uv run pytest -q                    # Tests
ruff check .                        # Linting
ruff format . --check               # Formatting
uv run basedpyright src tests       # Type checking
```

### Step 3: Tool-Specific Diagnosis

---

## pytest Errors

### Common Patterns

| Error | Cause | Fix |
|-------|-------|-----|
| `AssertionError` | Expected vs actual mismatch | Check test logic or implementation |
| `ImportError` | Missing dependency or wrong path | Install package or fix import |
| `AttributeError` | Method/attribute doesn't exist | Check API usage |
| `fixture not found` | Missing or misspelled fixture | Add fixture or fix name |
| `exit code 5` | No tests collected | Check test file naming (`test_*.py`) |

### Diagnosis Steps

1. **Read full error output** - Don't skim, read the entire traceback
2. **Identify failing test** - Note file:line and test name
3. **Check assertion** - Compare expected vs actual values
4. **Trace the problem**:
   - Is the test wrong? (Update test)
   - Is the implementation wrong? (Fix implementation)
5. **Re-run to verify**: `uv run pytest tests/path/to/test.py::test_name -q`

### Verification

```bash
uv run pytest -q                    # All tests pass
uv run pytest -q --tb=short         # With short tracebacks if debugging
```

---

## ruff Errors

### Common Error Codes

| Code | Meaning | Auto-fix? |
|------|---------|-----------|
| `E501` | Line too long | No - refactor needed |
| `F401` | Unused import | Yes |
| `F841` | Unused variable | Yes |
| `I001` | Import order wrong | Yes |
| `UP` | Use modern Python syntax | Usually yes |

### Diagnosis Steps

1. **Run with auto-fix first**: `ruff check . --fix`
2. **For remaining errors**, read the rule explanation
3. **For line length** (`E501`):
   - Break long strings
   - Use intermediate variables
   - Split long function calls
4. **Re-run to verify**: `ruff check .`

### Verification

```bash
ruff format .                       # Format code
ruff check . --fix                  # Fix what can be fixed
ruff check .                        # Verify clean (0 errors)
```

---

## basedpyright Errors

### Common Error Types

| Error | Cause | Fix |
|-------|-------|-----|
| `reportMissingTypeStubs` | No type stubs for library | Add stubs or ignore |
| `reportUnknownMemberType` | Can't infer type | Add type annotation |
| `reportArgumentType` | Wrong argument type | Fix argument or annotation |
| `reportReturnType` | Return type mismatch | Fix return or annotation |
| `reportUnboundVariable` | Variable used before assignment | Initialize or check logic |

### Diagnosis Steps

1. **Read error message and file:line**
2. **Check if type stub needed**: Some libraries lack stubs
3. **Add type annotations** where missing:
   ```python
   def func(arg: str) -> int:  # Add parameter and return types
   ```
4. **For complex types**, use typing module:
   ```python
   from typing import Any, Optional, Union
   ```
5. **Re-run to verify**: `uv run basedpyright src tests`

### Verification

```bash
uv run basedpyright src tests       # 0 errors
```

---

## uv Errors

### Common Error Types

| Error | Cause | Fix |
|-------|-------|-----|
| `ResolutionFailure` | Conflicting dependencies | Relax version constraints |
| `PackageNotFound` | Package doesn't exist | Check spelling, use correct name |
| `LockfileOutdated` | Lock file stale | Regenerate lock |
| `WheelBuildFailed` | Binary build failed | Check Python version, system deps |

### Diagnosis Steps

1. **For resolution failures**:
   - Check `pyproject.toml` version constraints
   - Try `uv lock --upgrade` to refresh
   - Relax overly strict version pins

2. **For missing packages**:
   - Verify package name on PyPI
   - Install: `uv pip install package-name`

3. **For lock file issues**:
   ```bash
   uv lock --upgrade
   ```

### Verification

```bash
uv pip list                         # Check installed packages
uv lock --check                     # Verify lock file is current
```

---

## Decision Tree

```
Build failed?
├─ pytest error?
│  ├─ AssertionError → Check test vs implementation
│  ├─ ImportError → Check imports and dependencies
│  └─ No tests collected → Check test naming (test_*.py)
│
├─ ruff error?
│  ├─ Auto-fixable? → Run `ruff check . --fix`
│  └─ Manual fix → Read rule, refactor code
│
├─ basedpyright error?
│  ├─ Missing types? → Add type annotations
│  ├─ Wrong types? → Fix implementation or annotation
│  └─ Missing stubs? → Add stubs or suppress
│
└─ uv error?
   ├─ Resolution failed? → Relax constraints, upgrade lock
   └─ Package not found? → Verify name on PyPI
```

## Full Verification Sequence

Run all checks in order:

```bash
# 1. Format
ruff format .

# 2. Lint
ruff check . --fix
ruff check .

# 3. Type check
uv run basedpyright src tests

# 4. Test
uv run pytest -q

# All must pass (0 errors, 0 failures)
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/businessplus-community) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
