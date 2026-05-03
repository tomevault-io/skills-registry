---
name: fastapi-best-practices
description: Best practices, patterns, and conventions for FastAPI development. Covers project structure, async/sync rules, Pydantic usage, and database interactions. Use when this capability is needed.
metadata:
  author: alxnko
---

# FastAPI Best Practices

> **Objective**: Build scalable, maintainable, and high-performance FastAPI applications.
> **Philosophy**: Standardize structure, enforce type safety, and optimize for I/O.

---

## 1. Project Structure

Organize code by **domain** (modules), not by technical layer (controllers/models).

### Recommended Layout

```
src/
├── auth/               # Domain: Authentication
│   ├── router.py       # API Endpoints
│   ├── service.py      # Business Logic
│   ├── schemas.py      # Pydantic Models
│   ├── models.py       # Database Models
│   ├── config.py       # Domain-specific Config
│   └── constants.py    # Domain Constants
├── posts/              # Domain: Posts
│   ├── router.py
│   └── ...
├── config.py           # Global Config
├── database.py         # DB Connection
├── main.py             # App Entrypoint
└── utils.py            # Shared Utilities
```

### Import Rules

- Use explicit absolute imports for cross-domain usage.
- Example: `from src.auth import constants as auth_constants`

---

## 2. Async vs Sync Routes

### 🟢 Use `async def` (Non-blocking)

Use for I/O-bound operations:

- Database queries (with async drivers like `asyncpg`)
- External API calls (using `httpx`)
- File I/O (using `aiofiles`)

```python
@router.get("/users")
async def get_users():
    return await database.fetch_all("SELECT * FROM users")
```

### 🔵 Use `def` (Blocking/Sync)

Use for CPU-bound operations or synchronous libraries. FastAPI runs these in a threadpool.

```python
@router.get("/compute")
def calculate_pi():
    # Heavy calculation
    return {"pi": 3.14159...}
```

### ⚠️ Common Pitfalls

- **NEVER** use `time.sleep()` in `async def`. It blocks the loop.
- **NEVER** call sync blocking code (like `requests` or standard `open()`) inside `async def`.
- If you MUST use a sync library in an async route, use `run_in_threadpool`.

```python
from fastapi.concurrency import run_in_threadpool

@router.get("/sync-lib")
async def call_sync_wrapper():
    result = await run_in_threadpool(sync_function, arg1)
    return result
```

---

## 3. Pydantic Best Practices

### Validation Rules

- **Excessive Validation**: Use Pydantic for all data boundaries (Request Body, Query Params, Response Models, Config).
- **Custom Base**: Create a `CustomModel` to standardize config (typically `populate_by_name=True`, datetime handling).

### Config/Settings

- Split `BaseSettings` by domain if the app is large.
- **Secrets**: Never hardcode. Use `.env` or environment variables.

```python
# src/auth/config.py
class AuthConfig(BaseSettings):
    JWT_SECRET: str
    JWT_EXP: int = 5
```

---

## 4. Dependencies & Injection

### Use Dependencies For:

- **Authentication**: `get_current_user`
- **Permissions**: `get_current_active_superuser`
- **Database Sessions**: `get_db`
- **Complex Validation**: Check if resource exists before entering the route.

### Chain Dependencies

Reuse logic by chaining. Dependencies are cached per request (called once even if used multiple times).

```python
async def valid_post_id(post_id: UUID) -> Post:
    # ... checks DB ...
    return post

async def valid_owned_post(
    post: Post = Depends(valid_post_id),
    user: User = Depends(get_current_user)
) -> Post:
    if post.owner_id != user.id:
        raise HTTPException(403)
    return post
```

---

## 5. Database & SQL

### Naming Conventions

- **Tables**: `lower_case_snake`, singular (e.g., `user`, `post`).
- **Indexes**: Explicit naming (e.g., `ix_user_email`).
- **Dates**: `created_at` (datetime), `birth_date` (date).

### SQL-First Approach

- Prefer SQL (or powerful ORM queries) over Python loops for data processing.
- Use Database to aggregate JSON if possible for complex nested structures.

---

## 6. Testing

### Async Test Client

Always use `httpx.AsyncClient` for testing `async` FastAPI apps.

```python
import pytest
from httpx import AsyncClient

@pytest.mark.asyncio
async def test_root(client: AsyncClient):
    response = await client.get("/")
    assert response.status_code == 200
```

---

## 7. Documentation (OpenAPI)

### Environment Awareness

- Hide docs (`/docs`, `/redoc`) in Production environments.
- Show them only in `local` or `staging`.

### Explicit Responses

- Document error responses using `responses={...}` in the route decorator.

```python
@router.post("/items", responses={404: {"description": "Item not found"}})
async def create_item(...): ...
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alxnko) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
