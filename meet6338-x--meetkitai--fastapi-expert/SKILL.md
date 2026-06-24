---
name: fastapi-expert
description: expert FastAPI development patterns. Async, Pydantic v2, dependency injection, and production deployment. Use when this capability is needed.
metadata:
  author: Meet6338-X
---

# FastAPI Expert

> **Goal**: Build high-performance, type-safe, and self-documenting APIs using modern Python.

## 1. Core Principles

- **Type Hints**: Use standard Python type hints everywhere.
- **Pydantic Models**: Separation of concerns.
    - `UserCreate` (Input)
    - `UserUpdate` (Partial Input)
    - `UserResponse` (Output - NO passwords!)
- **Dependency Injection**: Use `Depends` for DB sessions, auth users, and reusable logic.

## 2. API Structure

```
app/
├── api/
│   ├── v1/
│   │   ├── endpoints/
│   │   │   ├── users.py
│   │   │   └── items.py
│   │   └── router.py
│   └── deps.py         # Dependencies (get_current_user, get_db)
├── core/
│   ├── config.py       # Pydantic Settings
│   └── security.py     # JWT logic, password hashing
├── db/
│   ├── base.py
│   └── session.py
├── models/             # SQLAlchemy / Tortoise / SQLModel models
├── schemas/            # Pydantic models
└── main.py
```

## 3. Database Patterns (SQLAlchemy 2.0 / SQLModel)

- **Async Session**: Use `AsyncSession` for non-blocking I/O.
- **Allembic**: Mandatory for migrations. Never use `create_all` in production.
- **Context Managers**: Ensure DB sessions are closed.

```python
async def get_db():
    async with AsyncSessionLocal() as session:
        yield session
```

## 4. Authentication & Security

- **OAuth2**: Use `OAuth2PasswordBearer` for strict spec compliance.
- **JWT**: Stateless auth. Keep access tokens short-lived (15m), refresh tokens long (7d).
- **Hashing**: Use `passlib` with `bcrypt` or `argon2`.

## 5. Performance Tips

- **Uvicorn**: Run with `uvicorn main:app --workers 4 --loop uvloop`.
- **Async/Await**: properly await I/O bound operations. Don't block the event loop with heavy CPU tasks (use `run_in_threadpool`).
- **JSON Serialization**: Use `orjson` for faster JSON parsing/encoding.

## 6. Testing (Pytest)

- **AsyncClient**: Use `httpx.AsyncClient` for testing routes.
- **Override Dependencies**: Use `app.dependency_overrides` to mock DB or Auth during tests.

```python
async def test_create_item(async_client: AsyncClient):
    response = await async_client.post("/items/", json={"name": "Test"})
    assert response.status_code == 200
```

---
> Source: [Meet6338-X/MeetKitAI](https://github.com/Meet6338-X/MeetKitAI) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
