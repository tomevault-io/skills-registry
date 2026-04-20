---
name: fastapi
description: | Use when this capability is needed.
metadata:
  author: jamaliumair
---

# FastAPI Development Skill

Production-ready patterns for modern FastAPI backend development.

## Quick Reference

| Feature | Reference File |
|---------|----------------|
| Project setup, structure, UV, PM2 | [references/project-setup.md](references/project-setup.md) |
| JWT auth, OAuth2, Argon2 | [references/authentication.md](references/authentication.md) |
| SQLModel, relationships, migrations | [references/sqlmodel-orm.md](references/sqlmodel-orm.md) |
| MongoDB, Beanie ODM | [references/mongodb.md](references/mongodb.md) |
| Middleware, CORS, rate limiting | [references/middleware.md](references/middleware.md) |
| Pagination, responses, schemas | [references/responses.md](references/responses.md) |
| Events, observers, signals | [references/events.md](references/events.md) |
| Pytest, TDD, fixtures | [references/testing.md](references/testing.md) |
| Microservices patterns | [references/microservices.md](references/microservices.md) |

## Core Patterns

### Minimal FastAPI App

```python
from fastapi import FastAPI
from contextlib import asynccontextmanager

@asynccontextmanager
async def lifespan(app: FastAPI):
    # Startup
    yield
    # Shutdown

app = FastAPI(title="My API", lifespan=lifespan)

@app.get("/")
async def root():
    return {"message": "Hello World"}
```

### Standard Project Structure

```
project/
├── app/
│   ├── __init__.py
│   ├── main.py              # FastAPI app
│   ├── api/v1/              # Routers
│   ├── core/                # Config, security, database
│   ├── models/              # SQLModel/Pydantic models
│   ├── repositories/        # Data access layer
│   ├── services/            # Business logic
│   ├── schemas/             # Request/response schemas
│   └── middleware/          # Custom middleware
├── alembic/                 # Migrations
├── tests/
├── pyproject.toml
└── .env
```

### Config with Pydantic Settings

```python
from pydantic_settings import BaseSettings

class Settings(BaseSettings):
    APP_NAME: str = "FastAPI App"
    DEBUG: bool = False
    DATABASE_URL: str
    SECRET_KEY: str

    class Config:
        env_file = ".env"

settings = Settings()
```

### Dependency Injection Pattern

```python
from typing import Annotated
from fastapi import Depends
from sqlalchemy.ext.asyncio import AsyncSession

async def get_session() -> AsyncSession:
    async with async_session() as session:
        yield session

DbSession = Annotated[AsyncSession, Depends(get_session)]

@app.get("/items")
async def get_items(session: DbSession):
    # session is injected
    pass
```

### Repository Pattern

```python
class BaseRepository[T]:
    def __init__(self, session: AsyncSession, model: type[T]):
        self.session = session
        self.model = model

    async def get_by_id(self, id: int) -> T | None:
        return await self.session.get(self.model, id)

    async def create(self, data: dict) -> T:
        obj = self.model(**data)
        self.session.add(obj)
        await self.session.flush()
        return obj
```

## Feature Implementation Checklist

### Adding Authentication
1. Read [references/authentication.md](references/authentication.md)
2. Add security dependencies (python-jose, passlib, argon2-cffi)
3. Create `core/security.py` with JWT functions
4. Create `core/dependencies.py` with auth dependencies
5. Create User model and repository
6. Add auth router with login/register endpoints

### Adding Database (SQLModel)
1. Read [references/sqlmodel-orm.md](references/sqlmodel-orm.md)
2. Add dependencies (sqlmodel, asyncpg)
3. Create `core/database.py` with async engine
4. Define models with relationships
5. Set up Alembic for migrations
6. Create repositories for data access

### Adding Middleware
1. Read [references/middleware.md](references/middleware.md)
2. Add CORS middleware in main.py
3. Create custom middleware in `middleware/` folder
4. Register middleware in correct order (last added = first executed)

### Adding Tests
1. Read [references/testing.md](references/testing.md)
2. Add test dependencies (pytest, pytest-asyncio, httpx)
3. Create `tests/conftest.py` with fixtures
4. Write tests using async client

## Commands

```bash
# Development
fastapi dev app/main.py

# Production
fastapi run app/main.py

# With UV
uv run fastapi dev app/main.py

# With PM2
pm2 start ecosystem.config.js

# Migrations
alembic revision --autogenerate -m "message"
alembic upgrade head

# Tests
pytest -v
pytest --cov=app tests/
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jamaliumair) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
