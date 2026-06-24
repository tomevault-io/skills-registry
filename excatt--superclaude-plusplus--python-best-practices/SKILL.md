---
name: python-best-practices
description: Python code review and best practices validation. Comprehensive analysis including type hints, testing, linting, and package management. Use when this capability is needed.
metadata:
  author: excatt
---

# Python Best Practices Skill

## Purpose

Comprehensively analyze Python code quality, type safety, test coverage, and linting compliance.

## When to Use

- **Auto-execute** when Python code review requested
- When analyzing `.py` files
- When reviewing FastAPI, Django, Flask projects
- When inspecting package dependencies

## Analysis Categories

### 1. Type Hints (25%)

**Check Items**:
- Function parameter type hints
- Return type annotations
- Complex types (Generic, Union, Optional)
- TypedDict, Protocol usage

```python
# ❌ Bad
def get_user(id):
    return db.query(id)

# ✅ Good
def get_user(id: int) -> User | None:
    return db.query(id)
```

**Verification Tool**: `mypy --strict`

### 2. Code Quality (25%)

**Check Items**:
- PEP 8 style compliance
- Function/class complexity
- Import organization
- Docstring presence

```python
# ❌ Bad
def f(x,y,z): return x+y+z

# ✅ Good
def calculate_sum(a: int, b: int, c: int) -> int:
    """Calculate the sum of three integers."""
    return a + b + c
```

**Verification Tool**: `ruff check`, `ruff format --check`

### 3. Testing (20%)

**Check Items**:
- Test files exist (`tests/`, `*_test.py`)
- Test coverage (≥80% recommended)
- Fixture usage
- Mocking patterns

```python
# ✅ Good test structure
def test_get_user_returns_user(db_session: Session):
    user = create_user(db_session, name="test")
    result = get_user(user.id)
    assert result.name == "test"

def test_get_user_returns_none_for_invalid_id():
    result = get_user(999999)
    assert result is None
```

**Verification Tool**: `pytest --cov`

### 4. Security (15%)

**Check Items**:
- SQL Injection prevention
- Hardcoded secrets
- Unsafe deserialization
- Input validation

```python
# ❌ Bad
query = f"SELECT * FROM users WHERE id = {user_id}"

# ✅ Good
query = "SELECT * FROM users WHERE id = :id"
result = db.execute(query, {"id": user_id})
```

**Verification Tool**: `bandit`

### 5. Dependencies (15%)

**Required Rule**: **Use uv** (pip, poetry, pipenv prohibited)

**Check Items**:
- `pyproject.toml` (PEP 621 standard) exists
- `uv.lock` exists and committed
- Version ranges properly specified (`^`, `~`)
- Development dependency groups separated

```toml
# ✅ Good pyproject.toml (PEP 621 / uv)
[project]
name = "my-project"
version = "0.1.0"
requires-python = ">=3.11"
dependencies = []

[dependency-groups]
dev = [
    "pytest>=8.0",
    "mypy>=1.8",
    "ruff>=0.1",
]
```

```toml
# ❌ Bad - Using requirements.txt or Poetry format
[tool.poetry]
dependencies = {python = "^3.11"}

# ❌ Bad - Using poetry.lock
```

**Verification Tool**: `uv lock --check`, `uv sync --frozen`

**Docker Pattern**:
```dockerfile
COPY --from=ghcr.io/astral-sh/uv:latest /uv /uvx /bin/
COPY pyproject.toml uv.lock ./
RUN uv sync --frozen --no-dev
```

---

## Output Format

### High Quality (≥90%)
```
📋 Python Best Practices Check:
   ✅ Type Hints: 95% coverage (mypy strict pass)
   ✅ Code Quality: A (ruff 0 errors)
   ✅ Testing: 87% coverage (42 tests)
   ✅ Security: No issues (bandit clean)
   ✅ Dependencies: All pinned, no vulnerabilities

📊 Score: 0.94 (94%)
✅ Production Ready
```

### Needs Improvement (70-89%)
```
📋 Python Best Practices Check:
   ✅ Type Hints: 78% coverage
   ⚠️  Code Quality: B (12 ruff warnings)
   ✅ Testing: 72% coverage
   ⚠️  Security: 2 low-severity issues
   ✅ Dependencies: OK

📊 Score: 0.76 (76%)
⚠️  Review Recommended

💡 Improvements Needed:
1. src/utils.py:45 - Missing type hints
2. src/api.py:120 - High complexity (refactor recommended)
3. src/db.py:67 - Caution with SQL string formatting
```

### Poor Quality (<70%)
```
📋 Python Best Practices Check:
   ❌ Type Hints: 32% coverage
   ❌ Code Quality: D (47 errors)
   ❌ Testing: 15% coverage (3 tests)
   ⚠️  Security: 5 issues
   ❌ Dependencies: Unpinned versions

📊 Score: 0.42 (42%)
❌ Not Ready for Review

🚨 Critical Issues:
1. Insufficient type safety - mypy cannot run
2. Test coverage critically low
3. requirements.txt versions not pinned
```

---

## Verification Commands

```bash
# Run in uv environment

# Type checking
uv run mypy --strict src/

# Linting & formatting
uv run ruff check src/
uv run ruff format --check src/

# Testing
uv run pytest --cov=src --cov-report=term-missing

# Security
uv run bandit -r src/

# Dependencies
uv lock --check        # Check lock file sync
uv sync --frozen       # Verify install from lock
uv pip list --outdated # List updatable packages

# Virtual environment management
uv venv                # Show/create environment info
uv sync                # Install dependencies
uv lock --upgrade      # Upgrade dependencies
```

---

## Framework-Specific Checks

### FastAPI
```python
# ✅ Good patterns
from fastapi import Depends, HTTPException, status
from pydantic import BaseModel

class UserCreate(BaseModel):
    name: str
    email: EmailStr

@app.post("/users", response_model=UserResponse)
async def create_user(
    user: UserCreate,
    db: Session = Depends(get_db)
) -> UserResponse:
    ...
```

**Additional Checks**:
- Pydantic model usage
- Dependency injection pattern
- Async endpoints
- OpenAPI documentation

### Django
```python
# ✅ Good patterns
from django.db import models
from django.core.validators import MinLengthValidator

class User(models.Model):
    name = models.CharField(max_length=100, validators=[MinLengthValidator(2)])
    email = models.EmailField(unique=True)

    class Meta:
        indexes = [models.Index(fields=['email'])]
```

**Additional Checks**:
- Model indexes
- Migration state
- Security middleware
- CSRF protection

---

## Integration with Other Skills

```
/confidence-check     → Verify Python project architecture
    │
    ▼
/python-best-practices → Analyze code quality
    │
    ▼
/verify               → Verify build/tests
    │
    ▼
/learn                → Save patterns
```

---

## Commands

| Command | Description |
|---------|-------------|
| `/python-best-practices` | Full analysis |
| `/python-best-practices --quick` | Type/lint only |
| `/python-best-practices --security` | Security focus |
| `/python-best-practices --deps` | Dependencies focus |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/excatt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
