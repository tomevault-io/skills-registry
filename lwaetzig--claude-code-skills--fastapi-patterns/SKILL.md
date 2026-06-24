---
name: fastapi-patterns
description: > Use when this capability is needed.
metadata:
  author: LWaetzig
---

# FastAPI Patterns Skill

Build FastAPI applications that are well-structured, type-safe, and easy to maintain.

---

## Project Structure

For non-trivial APIs, use this layout:

```
app/
├── main.py              # App factory, lifespan, global middleware
├── api/
│   ├── deps.py          # Shared dependencies (get_db, get_current_user)
│   └── v1/
│       ├── router.py    # Aggregates all v1 routers
│       ├── users.py     # /users endpoints
│       └── items.py     # /items endpoints
├── models/
│   ├── user.py          # SQLAlchemy / SQLModel ORM models
│   └── item.py
├── schemas/
│   ├── user.py          # Pydantic request/response schemas
│   └── item.py
├── services/
│   ├── user_service.py  # Business logic
│   └── item_service.py
├── core/
│   ├── config.py        # Settings via pydantic-settings
│   ├── security.py      # Password hashing, JWT helpers
│   └── database.py      # Engine, SessionLocal, Base
└── tests/
```

---

## App Factory and Lifespan

```python
# main.py
from contextlib import asynccontextmanager
from fastapi import FastAPI
from app.api.v1.router import api_router
from app.core.database import engine, Base

@asynccontextmanager
async def lifespan(app: FastAPI):
    # Startup
    async with engine.begin() as conn:
        await conn.run_sync(Base.metadata.create_all)
    yield
    # Shutdown
    await engine.dispose()

def create_app() -> FastAPI:
    app = FastAPI(
        title="My API",
        version="1.0.0",
        lifespan=lifespan,
    )
    app.include_router(api_router, prefix="/api/v1")
    return app

app = create_app()
```

---

## Pydantic Schemas

Separate request schemas, response schemas, and DB models. Never return ORM models directly.

```python
# schemas/user.py
from pydantic import BaseModel, EmailStr, ConfigDict
from datetime import datetime

class UserCreate(BaseModel):
    email: EmailStr
    password: str
    name: str

class UserUpdate(BaseModel):
    name: str | None = None
    email: EmailStr | None = None

class UserResponse(BaseModel):
    model_config = ConfigDict(from_attributes=True)  # enables ORM mode

    id: int
    email: EmailStr
    name: str
    created_at: datetime

    # Never include: password, password_hash
```

### Use `model_config = ConfigDict(from_attributes=True)` on response schemas

This lets FastAPI serialize ORM objects directly: `UserResponse.model_validate(db_user)`.

---

## Dependency Injection

```python
# api/deps.py
from typing import Annotated
from fastapi import Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer
from sqlalchemy.ext.asyncio import AsyncSession
from app.core.database import get_async_session
from app.core.security import decode_token
from app.models.user import User

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="/api/v1/auth/token")

async def get_db() -> AsyncSession:
    async with get_async_session() as session:
        yield session

async def get_current_user(
    token: Annotated[str, Depends(oauth2_scheme)],
    db: Annotated[AsyncSession, Depends(get_db)],
) -> User:
    payload = decode_token(token)
    if not payload:
        raise HTTPException(status_code=status.HTTP_401_UNAUTHORIZED)
    user = await db.get(User, payload["sub"])
    if not user:
        raise HTTPException(status_code=status.HTTP_401_UNAUTHORIZED)
    return user

# Typed aliases — use these in route signatures
CurrentUser = Annotated[User, Depends(get_current_user)]
DbSession = Annotated[AsyncSession, Depends(get_db)]
```

---

## Router and Route Patterns

```python
# api/v1/users.py
from typing import Annotated
from fastapi import APIRouter, HTTPException, status
from app.api.deps import CurrentUser, DbSession
from app.schemas.user import UserCreate, UserResponse, UserUpdate
from app.services.user_service import UserService

router = APIRouter(prefix="/users", tags=["users"])

@router.get("/me", response_model=UserResponse)
async def get_current_user_profile(current_user: CurrentUser):
    return current_user

@router.get("/{user_id}", response_model=UserResponse)
async def get_user(user_id: int, db: DbSession):
    user = await UserService(db).get_by_id(user_id)
    if not user:
        raise HTTPException(status_code=status.HTTP_404_NOT_FOUND)
    return user

@router.post("/", response_model=UserResponse, status_code=status.HTTP_201_CREATED)
async def create_user(payload: UserCreate, db: DbSession):
    return await UserService(db).create(payload)

@router.patch("/{user_id}", response_model=UserResponse)
async def update_user(
    user_id: int,
    payload: UserUpdate,
    current_user: CurrentUser,
    db: DbSession,
):
    if current_user.id != user_id:
        raise HTTPException(status_code=status.HTTP_403_FORBIDDEN)
    return await UserService(db).update(user_id, payload)
```

---

## Error Handling

### Use `HTTPException` for expected errors

```python
raise HTTPException(
    status_code=status.HTTP_404_NOT_FOUND,
    detail="User not found",
)
```

### Custom exception handlers for domain errors

```python
# main.py
from fastapi import Request
from fastapi.responses import JSONResponse

class ResourceNotFoundError(Exception):
    def __init__(self, resource: str, id: int):
        self.resource = resource
        self.id = id

@app.exception_handler(ResourceNotFoundError)
async def not_found_handler(request: Request, exc: ResourceNotFoundError):
    return JSONResponse(
        status_code=404,
        content={"detail": f"{exc.resource} with id {exc.id} not found"},
    )
```

### Validation error format

FastAPI automatically returns 422 with field-level detail for Pydantic validation errors. Don't re-raise or wrap these.

---

## Settings

```python
# core/config.py
from pydantic_settings import BaseSettings, SettingsConfigDict

class Settings(BaseSettings):
    model_config = SettingsConfigDict(env_file=".env", env_file_encoding="utf-8")

    database_url: str
    secret_key: str
    access_token_expire_minutes: int = 30
    debug: bool = False

settings = Settings()  # reads from env / .env file
```

---

## Authentication (JWT)

```python
# core/security.py
from datetime import datetime, timedelta, timezone
from jose import JWTError, jwt
from passlib.context import CryptContext
from app.core.config import settings

pwd_context = CryptContext(schemes=["bcrypt"])

def hash_password(password: str) -> str:
    return pwd_context.hash(password)

def verify_password(plain: str, hashed: str) -> bool:
    return pwd_context.verify(plain, hashed)

def create_access_token(subject: int | str) -> str:
    expire = datetime.now(timezone.utc) + timedelta(minutes=settings.access_token_expire_minutes)
    return jwt.encode({"sub": str(subject), "exp": expire}, settings.secret_key, algorithm="HS256")

def decode_token(token: str) -> dict | None:
    try:
        return jwt.decode(token, settings.secret_key, algorithms=["HS256"])
    except JWTError:
        return None
```

---

## Background Tasks

```python
from fastapi import BackgroundTasks

@router.post("/orders/", status_code=201)
async def create_order(
    payload: OrderCreate,
    background_tasks: BackgroundTasks,
    db: DbSession,
):
    order = await OrderService(db).create(payload)
    background_tasks.add_task(send_order_confirmation_email, order.id)
    return order
```

Use background tasks for fire-and-forget work (emails, webhooks). For anything that must complete reliably, use a task queue (Celery, ARQ, or similar).

---

## Middleware

```python
from fastapi.middleware.cors import CORSMiddleware
from fastapi.middleware.gzip import GZipMiddleware
import time

app.add_middleware(
    CORSMiddleware,
    allow_origins=settings.allowed_origins,
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
app.add_middleware(GZipMiddleware, minimum_size=1000)

@app.middleware("http")
async def add_request_timing(request, call_next):
    start = time.perf_counter()
    response = await call_next(request)
    response.headers["X-Process-Time"] = str(time.perf_counter() - start)
    return response
```

---

## OpenAPI Customization

```python
@router.post(
    "/items/",
    response_model=ItemResponse,
    status_code=201,
    summary="Create a new item",
    response_description="The created item",
    responses={
        409: {"description": "Item with this name already exists"},
    },
)
async def create_item(payload: ItemCreate, db: DbSession):
    ...
```

Tag routers to group endpoints in the docs:
```python
router = APIRouter(prefix="/items", tags=["items"])
```

---

## Common Gotchas

- **Never use `async def` with blocking I/O** — use `run_in_executor` or switch to a sync route if the library isn't async-native
- **Dependency functions that `yield` must be async generators for async DB sessions** — `async def get_db()` with `yield`
- **`response_model` strips fields not in the schema** — use it as security, not just docs
- **Pydantic v2 uses `model_config`** not inner `Config` class — don't mix patterns
- **Avoid returning raw dicts** — always use `response_model` so the schema is enforced and documented

---
> Source: [LWaetzig/claude-code-skills](https://github.com/LWaetzig/claude-code-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
