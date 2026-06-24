---
name: fastapi-structure
description: Estructura y convenciones para proyectos FastAPI. Invocar cuando se arranca un backend nuevo, se agrega un router, o se diseña la organización de un proyecto REST. Use when this capability is needed.
metadata:
  author: xguilxr
---

# FastAPI — Estructura estándar

## Árbol del proyecto

```
app/
├── main.py              # FastAPI app + middleware + lifespan
├── config.py            # Settings con pydantic-settings
├── db.py                # Engine async + session maker
├── deps.py              # Dependencias reutilizables
├── models/
│   ├── __init__.py
│   ├── base.py          # Base declarativa SQLAlchemy
│   └── user.py
├── schemas/
│   ├── __init__.py
│   └── user.py          # UserCreate, UserRead, UserUpdate
├── routers/
│   ├── __init__.py
│   └── users.py
├── services/
│   └── user_service.py
└── tests/
    ├── conftest.py
    └── test_users.py
```

## `main.py` base

```python
from contextlib import asynccontextmanager
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
from app.routers import users
from app.config import settings

@asynccontextmanager
async def lifespan(app: FastAPI):
    # startup
    yield
    # shutdown

app = FastAPI(
    title=settings.app_name,
    version=settings.version,
    lifespan=lifespan,
)

app.add_middleware(
    CORSMiddleware,
    allow_origins=settings.cors_origins,
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

app.include_router(users.router)

@app.get("/health", tags=["health"])
async def health():
    return {"status": "ok"}
```

## Schemas: 3 variantes por recurso

```python
# schemas/user.py
from pydantic import BaseModel, EmailStr

class UserBase(BaseModel):
    email: EmailStr
    name: str

class UserCreate(UserBase):
    password: str

class UserUpdate(BaseModel):
    name: str | None = None

class UserRead(UserBase):
    id: int
    created_at: datetime

    model_config = {"from_attributes": True}
```

## Router thin

```python
# routers/users.py
from fastapi import APIRouter, Depends, HTTPException
from app.schemas.user import UserCreate, UserRead
from app.services import user_service
from app.deps import get_db, get_current_user

router = APIRouter(prefix="/users", tags=["users"])

@router.post("/", response_model=UserRead, status_code=201)
async def create_user(payload: UserCreate, db=Depends(get_db)):
    if await user_service.email_exists(db, payload.email):
        raise HTTPException(409, "Email already registered")
    return await user_service.create(db, payload)

@router.get("/{user_id}", response_model=UserRead)
async def get_user(user_id: int, db=Depends(get_db)):
    user = await user_service.get(db, user_id)
    if not user:
        raise HTTPException(404, "User not found")
    return user
```

## Service fat

```python
# services/user_service.py
from sqlalchemy.ext.asyncio import AsyncSession
from sqlalchemy import select
from app.models.user import User
from app.schemas.user import UserCreate

async def create(db: AsyncSession, payload: UserCreate) -> User:
    user = User(
        email=payload.email,
        name=payload.name,
        hashed_password=hash_password(payload.password),
    )
    db.add(user)
    await db.commit()
    await db.refresh(user)
    return user

async def get(db: AsyncSession, user_id: int) -> User | None:
    result = await db.execute(select(User).where(User.id == user_id))
    return result.scalar_one_or_none()
```

## Deps comunes

```python
# deps.py
from fastapi import Depends, HTTPException, status
from sqlalchemy.ext.asyncio import AsyncSession
from app.db import AsyncSessionLocal

async def get_db() -> AsyncSession:
    async with AsyncSessionLocal() as session:
        yield session

async def get_current_user(token: str = Depends(oauth2_scheme)):
    # ... decodificar JWT, buscar user
    pass
```

## Reglas

- **Router NUNCA toca la DB directamente.** Siempre vía service.
- **Response model siempre declarado** en cada endpoint.
- **Status codes explícitos** (`status_code=201` en POST).
- **Errores con detail claro**, no `{"error": "oops"}`.
- **Paginación** en GET que devuelven listas: `skip: int = 0, limit: int = Query(50, le=100)`.
- **Tags** en todos los routers para que OpenAPI se vea organizado.

---
> Source: [xguilxr/claudio-enterprises](https://github.com/xguilxr/claudio-enterprises) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
