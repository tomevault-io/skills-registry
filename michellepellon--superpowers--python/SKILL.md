---
name: python
description: > Use when this capability is needed.
metadata:
  author: michellepellon
---

# Python Development Skill

This skill provides expert-level Python development practices for building production systems.

## When This Skill Activates

- Writing or reviewing Python code
- Designing typed APIs and data models
- Building data pipelines or ETL workflows
- Creating async services or web APIs
- Optimizing Python performance
- Setting up Python project tooling

## Core Principles

1. **Types as documentation** — Public APIs are fully typed; `Any` is a code smell
2. **Static analysis gates** — `pyright` and `ruff` block merges, not just warn
3. **Reproducible environments** — `uv` + `pyproject.toml` as single source of truth
4. **Measure before optimizing** — Profile first, then fix the actual bottleneck
5. **Explicit over magical** — Prefer boring, readable code over clever metaprogramming

## Quick Reference

### Project Setup

```bash
# Initialize new project
uv init my-project
cd my-project

# Add dependencies
uv add fastapi pydantic httpx

# Add dev dependencies
uv add --dev pytest pyright ruff

# Sync environment
uv sync
```

### Required pyproject.toml Configuration

```toml
[project]
requires-python = ">=3.14"

[tool.pyright]
pythonVersion = "3.14"
typeCheckingMode = "strict"

[tool.ruff]
target-version = "py314"
line-length = 100

[tool.ruff.lint]
select = ["E", "F", "I", "N", "UP", "B", "A", "C4", "PT", "RUF"]
```

### Type Annotations Pattern

```python
from typing import Protocol, Self
from collections.abc import Sequence, Mapping

# Use protocols for duck typing
class Serializable(Protocol):
    def to_dict(self) -> Mapping[str, object]: ...

# Use Self for fluent APIs
class Builder:
    def with_name(self, name: str) -> Self:
        self._name = name
        return self

# Prefer collections.abc over typing module
def process(items: Sequence[str]) -> list[str]:
    return [item.upper() for item in items]
```

### FastAPI Service Pattern

```python
from fastapi import FastAPI, HTTPException, status
from pydantic import BaseModel, Field
import httpx

app = FastAPI()

class CreateUserRequest(BaseModel):
    email: str = Field(..., pattern=r"^[\w\.-]+@[\w\.-]+\.\w+$")
    name: str = Field(..., min_length=1, max_length=100)

class UserResponse(BaseModel):
    id: str
    email: str
    name: str

@app.post("/users", response_model=UserResponse, status_code=status.HTTP_201_CREATED)
async def create_user(request: CreateUserRequest) -> UserResponse:
    # Implementation with proper error handling
    ...
```

### Async with Proper Lifecycle

```python
import asyncio
from contextlib import asynccontextmanager
from collections.abc import AsyncIterator

@asynccontextmanager
async def managed_client() -> AsyncIterator[httpx.AsyncClient]:
    """Always use context managers for async resources."""
    async with httpx.AsyncClient(timeout=30.0) as client:
        yield client

async def fetch_with_timeout(url: str) -> bytes:
    """Explicit timeouts, never fire-and-forget."""
    async with managed_client() as client:
        response = await client.get(url)
        response.raise_for_status()
        return response.content
```

### Data Pipeline Pattern (Polars + Prefect)

```python
import polars as pl
from prefect import flow, task

@task(retries=3, retry_delay_seconds=60)
def extract_data(path: str) -> pl.LazyFrame:
    return pl.scan_parquet(path)

@task
def transform_data(lf: pl.LazyFrame) -> pl.LazyFrame:
    return lf.filter(pl.col("status") == "active").select(
        pl.col("id"),
        pl.col("value").cast(pl.Float64),
    )

@flow(log_prints=True)
def etl_pipeline(input_path: str, output_path: str) -> None:
    raw = extract_data(input_path)
    transformed = transform_data(raw)
    transformed.collect().write_parquet(output_path)
```

## Decision Framework

### When to Use What

| Need | Tool/Approach |
|------|---------------|
| DataFrames (analytics) | Polars (lazy mode) |
| DataFrames (compatibility) | pandas (explicit choice) |
| Local SQL analytics | DuckDB |
| Columnar interchange | Apache Arrow |
| HTTP client | httpx (with timeouts) |
| Web API | FastAPI + Pydantic v2 |
| Task orchestration | Prefect |
| Package management | uv only |
| Type checking | pyright (strict mode) |
| Linting + formatting | ruff |

### Concurrency Model Selection

| Workload | Model |
|----------|-------|
| I/O-bound, many connections | asyncio |
| CPU-bound, parallelizable | multiprocessing |
| CPU-bound, need shared state | threading (with locks) |
| Mixed I/O + CPU | ProcessPoolExecutor from async |

## Anti-Patterns to Avoid

1. **Untyped public APIs** — Every public function needs type hints
2. **Bare `except:`** — Always catch specific exceptions
3. **Fire-and-forget tasks** — Use `asyncio.TaskGroup` for lifecycle management
4. **Mutable default arguments** — Use `None` and initialize inside function
5. **requirements.txt as source of truth** — Use `pyproject.toml` + lockfile
6. **Implicit timeouts** — Every network call needs explicit timeout
7. **pandas for everything** — Use Polars for performance, pandas for compatibility

## Testing Strategy

```python
import pytest
from hypothesis import given, strategies as st

# Unit test for logic
def test_normalize_email() -> None:
    assert normalize_email("USER@Example.COM") == "user@example.com"

# Property-based test for invariants
@given(st.emails())
def test_email_normalization_is_idempotent(email: str) -> None:
    normalized = normalize_email(email)
    assert normalize_email(normalized) == normalized

# Integration test with real dependencies
@pytest.mark.integration
async def test_user_creation_flow(test_db: Database) -> None:
    user = await create_user(test_db, email="test@example.com")
    assert user.id is not None
```

## Detailed References

For comprehensive coverage of specific topics, see:

- [references/typing.md](references/typing.md) — Advanced typing patterns (PEPs 484, 544, 589, 695)
- [references/async.md](references/async.md) — Concurrency deep dive, structured concurrency
- [references/data-engineering.md](references/data-engineering.md) — Pipeline patterns, Prefect flows
- [references/performance.md](references/performance.md) — Profiling, optimization, native extensions
- [references/security.md](references/security.md) — Supply chain, secrets, input validation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/michellepellon) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
