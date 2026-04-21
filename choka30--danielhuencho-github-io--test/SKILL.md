---
name: test
description: Execute test suite with specified scope (unit, module, integration, all, or affected). Reports results and coverage. Use to verify code changes. Can auto-trigger during task execution or when verification is needed. Use when this capability is needed.
metadata:
  author: choka30
---

# Test

Execute test suite with specified scope.

## Scope Options

| Scope | Usage | Coverage |
|-------|-------|----------|
| `unit` | `/test unit <test_file>` | Single test file |
| `module` | `/test module <module_name>` | All tests for a module |
| `integration` | `/test integration` | Cross-module tests |
| `all` | `/test all` | Full test suite |
| `affected` | `/test affected` | Tests related to recent changes |

## Execution Commands

### Unit (Single File)
```bash
poetry run pytest tests/unit/test_<name>.py -v
```

### Module
```bash
poetry run pytest tests/unit/test_<module>*.py -v --cov=src/<module>
```

### Integration
```bash
poetry run pytest tests/integration/ -v
```

### All
```bash
poetry run pytest tests/ -v --cov=src --cov-report=term-missing
```

### Affected (Smart Selection)
```bash
# Get changed files
git diff --name-only HEAD~1

# Map to test files and run
poetry run pytest <affected_test_files> -v
```

## Output Format

### Success
```
═══════════════════════════════════════════════════════
  🧪 TEST RESULTS: [scope]
═══════════════════════════════════════════════════════

Passed:  NN ✓
Failed:  0
Skipped: N

Coverage: NN%

All tests passing.
```

### Failure
```
═══════════════════════════════════════════════════════
  🧪 TEST RESULTS: [scope]
═══════════════════════════════════════════════════════

Passed:  NN ✓
Failed:  N ✗
Skipped: N

Coverage: NN%

Failed Tests:
─────────────────────────────────────────────────────────
1. tests/test_file.py::test_name
   Error: [brief error message]
   
   Suggestion: [potential fix]
─────────────────────────────────────────────────────────

Action Required: Fix failing tests before proceeding.
```

## Coverage Targets

| Type | Minimum | Target |
|------|---------|--------|
| Unit tests | 80% | 90% |
| Integration | 60% | 75% |
| Critical paths | 100% | 100% |

## Test Naming Convention

```
test_<unit>_<behavior>_<condition>

Examples:
- test_parse_config_returns_dict_when_valid
- test_parse_config_raises_when_file_missing
- test_user_authenticate_returns_none_when_expired
```

## When to Run

| Situation | Scope |
|-----------|-------|
| After implementing a task | `affected` or `module` |
| Before commit | `affected` |
| Before push | `all` |
| After merge | `all` |
| Debugging specific test | `unit` |

## Debugging Failed Tests

```bash
# Run with more output
poetry run pytest tests/test_file.py::test_name -v --tb=long

# Run with debugger
poetry run pytest tests/test_file.py::test_name -v --pdb

# Run only last failed
poetry run pytest --lf -v
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/choka30) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
