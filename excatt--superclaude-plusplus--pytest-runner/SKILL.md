---
name: pytest-runner
description: Execute and analyze pytest-based tests. Includes coverage reports, failure analysis, and fixture debugging. Use when this capability is needed.
metadata:
  author: excatt
---

# Pytest Runner Skill

## Purpose

Execute and analyze tests for Python projects.

## When to Use

- **Auto-invoke** when test execution is requested
- Triggered by keywords: "run tests", "pytest", "test"
- Local test verification before CI/CD

## Execution Modes

### 1. Quick Test
```bash
pytest -x -q
# Stop at first failure, minimal output
```

### 2. Full Test with Coverage
```bash
pytest --cov=src --cov-report=term-missing --cov-report=html
# Full test suite + coverage report
```

### 3. Specific Test
```bash
pytest tests/test_api.py::test_create_user -v
# Run specific test only
```

### 4. Failed Only
```bash
pytest --lf
# Re-run only last failed tests
```

---

## Output Format

### All Pass
```
🧪 PYTEST RESULTS
=================

✅ All tests passed!

📊 Summary:
   Tests: 47 passed
   Time: 3.2s
   Coverage: 87%

📈 Coverage by Module:
   src/api.py        92%
   src/models.py     95%
   src/utils.py      78%  ⚠️
   src/db.py         85%
```

### With Failures
```
🧪 PYTEST RESULTS
=================

❌ 3 tests failed

📊 Summary:
   Tests: 44 passed, 3 failed
   Time: 4.1s

🔴 Failures:

1. test_api.py::test_create_user_invalid_email
   ├─ Expected: ValidationError
   └─ Got: None

   💡 Fix: Check email validation logic
   📍 Location: src/api.py:45

2. test_db.py::test_connection_timeout
   ├─ Expected: TimeoutError after 5s
   └─ Got: Hung indefinitely

   💡 Fix: Verify DB connection timeout setting

3. test_utils.py::test_parse_date
   ├─ Expected: datetime(2024, 1, 15)
   └─ Got: ValueError

   💡 Fix: Check date format parsing logic
```

---

## Coverage Analysis

### Minimum Thresholds
```yaml
coverage:
  minimum: 80%      # Overall minimum
  critical_paths:
    - src/api.py: 90%
    - src/auth.py: 95%
    - src/models.py: 85%
```

### Uncovered Lines Report
```
📋 Uncovered Code:

src/utils.py (78% covered):
   Lines 45-52: Error handling branch
   Lines 78-85: Edge case for empty input

   💡 Tests needed:
   - test_parse_with_empty_input()
   - test_parse_with_invalid_format()
```

---

## Fixture Analysis

### Fixture Dependencies
```
📦 Fixture Graph:

db_session
  └─→ test_user
      └─→ authenticated_client
          └─→ admin_client

⚠️  Warning: Deep fixture chain (4 levels)
    → Verify test isolation
```

### Slow Fixtures
```
⏱️  Slow Fixtures:

| Fixture | Avg Time | Usage |
|---------|----------|-------|
| db_session | 0.8s | 32 tests |
| redis_client | 0.5s | 12 tests |
| mock_api | 0.3s | 8 tests |

💡 Optimization: Consider changing db_session to module scope
```

---

## Integration

```bash
# Pre-commit hook
pytest --co -q && pytest -x

# CI pipeline
pytest --cov --cov-fail-under=80 --junitxml=report.xml

# Watch mode (with pytest-watch)
ptw -- --lf
```

---

## Commands

| Command | Description |
|---------|-------------|
| `/pytest` | Run all tests |
| `/pytest --quick` | Quick test (-x -q) |
| `/pytest --cov` | Include coverage |
| `/pytest --failed` | Re-run failed only |
| `/pytest [path]` | Run specific path |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/excatt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
