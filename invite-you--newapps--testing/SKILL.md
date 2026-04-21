---
name: testing
description: Testing Use when this capability is needed.
metadata:
  author: invite-you
---

# Testing

## Requirements
- pytest for unit/integration tests
- pytest-cov for coverage
- Playwright (Python) only when E2E is needed

## Commands
```bash
python -m pytest
python -m pytest --cov=. --cov-report=term-missing
```

## Coverage
- Minimum 80% for new code

## Test types
- Unit tests for pure functions
- Integration tests for FastAPI routes using httpx.AsyncClient

Example:
```python
import pytest
from httpx import AsyncClient
from app.main import app

@pytest.mark.asyncio
async def test_health():
    async with AsyncClient(app=app, base_url="http://test") as client:
        resp = await client.get("/health")
    assert resp.status_code == 200
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/invite-you) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
