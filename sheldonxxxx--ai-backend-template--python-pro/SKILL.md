---
name: python-pro
description: Professional Python development best practices using uv for package management, PEP 8 standards, type hints, testing, and secure coding patterns. Use when this capability is needed.
metadata:
  author: sheldonxxxx
---

# Python Pro Development Guidelines

## uv Usage Rules

**Always use uv for Python operations:**

| Task | Command |
|------|---------|
| Install package | `uv add package` |
| Install dev dependency | `uv add --dev package` |
| Remove package | `uv remove package` |
| Run script | `uv run python script.py` |
| Run with deps | `uv run --with package script.py` |
| Run tests | `uv run pytest` |
| Install from lock | `uv sync` |
| Generate lock file | `uv lock` |
| Create virtual env | `uv venv` |
| Add to pyproject.toml | `uv add` (auto-updates pyproject.toml) |

**Never use:** `pip`, `pip install`, `python -m venv`, `requirements.txt`

```bash
# Setup project
uv venv
uv sync

# Quick operations
uv add requests pydantic
uv add --dev pytest black ruff mypy
uv run pytest
uv run python main.py
```

## Documentation Lookup

**Before implementing with any library, always check its official documentation:**

1. Use context7 to look up library documentation
2. Verify correct API usage, parameters, and return types
3. Check for latest version changes and deprecations
4. Reference examples from official docs

```bash
# Example: Look up pydantic v2 documentation before using models
# Use context7 query: "pydantic v2 model validation field types"
```

## Code Style (PEP 8)

- 4 spaces, no tabs
- Lines: 79 chars (code), 72 chars (docstrings)
- Blank lines: 2 between classes/functions, 1 between methods

```python
class DataProcessor:
    """Processes data for analysis."""

    def __init__(self, config: dict):
        self.config = config
        self.cache: dict = {}

    def transform(self, data: list) -> list:
        """Transform input data."""
        return [item for item in data if item]

def main() -> None:
    """Entry point."""
    pass
```

**Naming:** `snake_case` (variables/functions), `CapWords` (classes), `ALL_CAPS` (constants)

## Project Structure

```
project/
├── src/package_name/
│   ├── __init__.py
│   ├── core/
│   ├── api/
│   ├── models/
│   └── utils/
├── tests/
│   ├── unit/
│   └── integration/
├── pyproject.toml
└── README.md
```

**pyproject.toml:**
```toml
[project]
name = "project_name"
version = "1.0.0"
requires-python = ">=3.10"
dependencies = [
    "requests>=2.31.0",
    "pydantic>=2.0.0",
]

[project.optional-dependencies]
dev = [
    "pytest>=7.4.0",
    "pytest-cov>=4.1.0",
    "ruff>=0.1.0",
]

[tool.pytest.ini_options]
testpaths = ["tests"]

[tool.ruff]
line-length = 88
```

## Type Hints

Use type hints for all function signatures.

```python
from typing import List, Dict, Optional, Union, Any
from dataclasses import dataclass
from enum import Enum

class Status(Enum):
    PENDING = "pending"
    ACTIVE = "active"

@dataclass
class User:
    id: int
    name: str
    email: str
    status: Status = Status.PENDING
    preferences: Optional[Dict[str, Any]] = None

    def validate(self) -> bool:
        return bool(self.id and self.name and self.email)

def process_users(
    users: List[User],
    filter_func: Optional[callable] = None
) -> Dict[str, List[User]]:
    result: Dict[str, List[User]] = {}
    for user in users:
        if filter_func and not filter_func(user):
            continue
        status = user.status.value
        result.setdefault(status, []).append(user)
    return result
```

## Error Handling

Create custom exception hierarchy.

```python
class AppError(Exception):
    def __init__(self, message: str, details: dict = None):
        super().__init__(message)
        self.message = message
        self.details = details or {}

class ValidationError(AppError):
    pass

class NotFoundError(AppError):
    pass

# Usage
def get_user(user_id: int) -> User:
    user = repository.find_by_id(user_id)
    if user is None:
        raise NotFoundError("User", str(user_id))
    return user
```

**Never use bare `except:` - catch specific exceptions.**

## Testing (pytest)

```python
import pytest
from dataclasses import dataclass

@dataclass
class Order:
    id: int
    total: float

class TestOrderProcessing:
    @pytest.fixture
    def sample_order(self):
        return Order(id=1, total=29.99)

    def test_total_calculation(self, sample_order):
        assert sample_order.total == 29.99

    @pytest.mark.parametrize('input,expected', [(1,1), (2,4), (3,9)])
    def test_square(self, input, expected):
        assert square(input) == expected
```

## Security

**Input validation:**
```python
import re
from typing import Optional

EMAIL_PATTERN = re.compile(r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$')

def validate_email(email: str) -> tuple[bool, list[str]]:
    errors = []
    if not email:
        errors.append("Email required")
    elif len(email) > 254:
        errors.append("Email too long")
    elif not EMAIL_PATTERN.match(email):
        errors.append("Invalid email format")
    return len(errors) == 0, errors
```

**Secrets - use environment variables:**
```python
import os
from dataclasses import dataclass

@dataclass
class Config:
    api_key: str
    debug: bool = False

    @classmethod
    def from_env(cls) -> 'Config':
        return cls(
            api_key=os.environ["API_KEY"],
            debug=os.environ.get("DEBUG", "false").lower() == "true"
        )
```

## Documentation

Google-style docstrings:

```python
def fetch_data(endpoint: str, timeout: int = 30) -> dict:
    """Fetch data from API endpoint.

    Args:
        endpoint: API endpoint URL.
        timeout: Request timeout in seconds.

    Returns:
        Response data dictionary.

    Raises:
        HTTPError: If request fails.
    """
    import requests
    response = requests.get(endpoint, timeout=timeout)
    response.raise_for_status()
    return response.json()
```

## Performance

**Use generators for large data:**
```python
def process_large_file(filepath: str, chunk_size: int = 10000):
    with open(filepath, 'r') as f:
        chunk = []
        for line in f:
            chunk.append(line)
            if len(chunk) >= chunk_size:
                yield chunk
                chunk = []
        if chunk:
            yield chunk
```

**Use `lru_cache` for expensive computations:**
```python
from functools import lru_cache

@lru_cache(maxsize=256)
def expensive_computation(x: int) -> int:
    return x * x + 1
```

## Async Programming

```python
import asyncio
from typing import List

class AsyncManager:
    def __init__(self, max_concurrent: int = 100):
        self.semaphore = asyncio.Semaphore(max_concurrent)

    async def execute(self, coro) -> tuple[bool, Any]:
        async with self.semaphore:
            try:
                result = await asyncio.wait_for(coro, timeout=30.0)
                return True, result
            except TimeoutError:
                return False, None
            except Exception as e:
                return False, e

    async def execute_all(self, tasks: List[asyncio.coroutine]):
        return await asyncio.gather(*tasks, return_exceptions=True)
```

## Logging

```python
import logging
import json
from datetime import datetime

class JSONFormatter(logging.Formatter):
    def format(self, record: logging.LogRecord) -> str:
        return json.dumps({
            "timestamp": datetime.utcnow().isoformat(),
            "level": record.levelname,
            "message": record.getMessage(),
            "logger": record.name
        })

def setup_logging(level: str = "INFO") -> None:
    logger = logging.getLogger()
    logger.setLevel(getattr(logging, level.upper()))
    handler = logging.StreamHandler()
    handler.setFormatter(JSONFormatter())
    logger.addHandler(handler)
```

## Code Review Checklist

- [ ] Code follows PEP 8
- [ ] Type hints on all functions
- [ ] Docstrings complete
- [ ] Tests cover new code
- [ ] No hardcoded secrets
- [ ] Specific exception handling
- [ ] Formatted (ruff/black)
- [ ] Type checking passes (mypy)
- [ ] No TODO comments in production

## Quick Reference

```bash
# Setup
uv venv
uv sync

# Dependencies
uv add requests pydantic
uv add --dev pytest ruff

# Run
uv run pytest
uv run python main.py

# Scripts in pyproject.toml
[project.scripts]
myapp = "package.cli:main"
```

```python
# Singleton
class Singleton:
    _instance = None
    def __new__(cls):
        if cls._instance is None:
            cls._instance = super().__new__(cls)
        return cls._instance

# Factory
class Factory:
    _registry = {}
    @classmethod
    def register(cls, name: str, cls_):
        cls._registry[name] = cls_
    @classmethod
    def create(cls, name: str, *args, **kwargs):
        return cls._registry[name](*args, **kwargs)

# Context manager
from contextlib import contextmanager

@contextmanager
def timer():
    import time
    start = time.time()
    try:
        yield
    finally:
        print(f"took {time.time() - start:.2f}s")
```

## Resources

- [Python Docs](https://docs.python.org/3/)
- [PEP 8](https://pep8.org/)
- [Type Hints](https://docs.python.org/3/library/typing.html)
- [pytest](https://docs.pytest.org/)
- [uv](https://docs.astral.sh/uv/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sheldonxxxx) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
