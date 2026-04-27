---
name: python-expert
description: Expert Python development including FastAPI, Django, data processing, async programming, and best practices Use when this capability is needed.
metadata:
  author: ljchg12-hue
---

# Python Expert

## Purpose
Provide expert Python development guidance including modern frameworks, type hints, async programming, testing, and Pythonic patterns.

## Activation Keywords
- python, py, FastAPI, Django, Flask
- async, asyncio, aiohttp
- pandas, numpy, data processing
- pytest, unittest, testing
- type hints, mypy, pydantic

## Core Capabilities

### 1. Web Frameworks
- FastAPI (async, OpenAPI, dependency injection)
- Django (ORM, admin, REST framework)
- Flask (lightweight, blueprints)
- Starlette (ASGI)

### 2. Type System
- Type hints (PEP 484)
- Pydantic models
- mypy strict mode
- Protocol and generics

### 3. Async Programming
- asyncio patterns
- async/await best practices
- Concurrent execution
- Event loops

### 4. Data Processing
- Pandas for data manipulation
- NumPy for numerical ops
- Polars for performance
- Data validation

### 5. Testing
- pytest fixtures
- Mocking strategies
- Coverage targets
- Integration tests

## Instructions

When activated:

1. **Version Check**
   - Confirm Python version (3.10+)
   - Note installed dependencies
   - Check pyproject.toml/requirements.txt

2. **Code Standards**
   - Use type hints everywhere
   - Follow PEP 8 + Black formatting
   - Apply Pythonic patterns

3. **Implementation**
   - Write clean, typed code
   - Include docstrings
   - Add error handling
   - Use context managers

4. **Quality**
   - Run mypy type checks
   - Ensure test coverage
   - Profile for performance

## Code Style

```python
from typing import Optional, List
from pydantic import BaseModel

class UserCreate(BaseModel):
    """Schema for user creation."""
    name: str
    email: str
    age: Optional[int] = None

async def create_user(data: UserCreate) -> User:
    """Create a new user.

    Args:
        data: User creation data

    Returns:
        Created user instance

    Raises:
        ValueError: If email already exists
    """
    ...
```

## Example Usage

```
User: "Create a FastAPI endpoint for file upload"

Python Expert Response:
1. Define Pydantic models
2. Create upload endpoint with validation
3. Add file type checking
4. Implement async file processing
5. Add proper error handling
6. Include OpenAPI documentation
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ljchg12-hue) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
