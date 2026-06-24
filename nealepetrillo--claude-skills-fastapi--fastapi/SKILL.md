---
name: fastapi
description: > Use when this capability is needed.
metadata:
  author: nealepetrillo
---

# FastAPI

FastAPI is a modern Python web framework built on Starlette (ASGI) and Pydantic (validation). Install with `pip install "fastapi[standard]"`.

## Architecture

```
FastAPI (routing, OpenAPI, DI) -> Starlette (ASGI, middleware, responses) -> Uvicorn (ASGI server)
                                  Pydantic (validation, serialization)
```

- **Starlette**: provides Request, Response, WebSocket, middleware, TestClient, StaticFiles
- **Pydantic**: provides BaseModel, Field, validation, serialization
- **Uvicorn**: ASGI server (`fastapi run` or `uvicorn app:app`)

## Quick Start

```python
from contextlib import asynccontextmanager
from fastapi import FastAPI, Depends, HTTPException, status
from pydantic import BaseModel

@asynccontextmanager
async def lifespan(app: FastAPI):
    # startup
    yield
    # shutdown

app = FastAPI(lifespan=lifespan)

class Item(BaseModel):
    name: str
    price: float
    description: str | None = None

@app.get("/items/{item_id}")
async def read_item(item_id: int, q: str | None = None):
    return {"item_id": item_id, "q": q}

@app.post("/items/", status_code=status.HTTP_201_CREATED)
async def create_item(item: Item):
    return item
```

## Core Patterns

### Parameter resolution

1. Declared in path string -> **path parameter**
2. Singular type (int, float, str, bool) -> **query parameter**
3. Pydantic `BaseModel` -> **request body**
4. `UploadFile` / `bytes` with `File()` -> **file upload**

Use `Query()`, `Path()`, `Header()`, `Cookie()`, `Body()`, `Form()`, `File()` for validation constraints and metadata.

### Dependency injection

```python
from typing import Annotated
from fastapi import Depends

async def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()

DBDep = Annotated[Session, Depends(get_db)]

@app.get("/items/")
async def read_items(db: DBDep):
    return db.query(Item).all()
```

- `yield` dependencies: code before yield = setup, after = cleanup (runs after response)
- Dependencies are cached per-request by default (`use_cache=True`)
- Execution order: router deps -> decorator deps -> parameter deps
- Always re-raise exceptions in `except` blocks after `yield`

### Response models (input/output separation)

```python
class UserBase(BaseModel):
    username: str
    email: str

class UserCreate(UserBase):
    password: str

class UserPublic(UserBase):
    id: int

@app.post("/users/", response_model=UserPublic)
async def create_user(user: UserCreate):
    # password is filtered out of response by response_model
    return save_user(user)
```

Prefer separate models over `response_model_include`/`response_model_exclude`.

### Error handling

```python
from fastapi import HTTPException

raise HTTPException(status_code=404, detail="Not found")  # detail can be any JSON-serializable value
raise HTTPException(status_code=403, detail={"error": "forbidden", "code": "AUTH_01"}, headers={"X-Error": "custom"})
```

Custom exception handlers:

```python
@app.exception_handler(MyException)
async def handler(request: Request, exc: MyException):
    return JSONResponse(status_code=418, content={"message": str(exc)})
```

### Application structure

```
app/
├── __init__.py
├── main.py            # FastAPI app, include_router calls
├── config.py          # Settings (pydantic-settings)
├── dependencies.py    # Shared dependencies
├── models.py          # Pydantic/SQLModel models
└── routers/
    ├── __init__.py
    ├── items.py       # APIRouter(prefix="/items", tags=["items"])
    └── users.py
```

```python
# routers/items.py
from fastapi import APIRouter, Depends
router = APIRouter(prefix="/items", tags=["items"], dependencies=[Depends(verify_token)])

@router.get("/")  # path does NOT include prefix
async def list_items(): ...

# main.py
app.include_router(items.router)
app.include_router(admin.router, prefix="/admin", tags=["admin"])
```

### Middleware

```python
# Function-based
@app.middleware("http")
async def timing_middleware(request: Request, call_next):
    start = time.perf_counter()
    response = await call_next(request)
    response.headers["X-Process-Time"] = str(time.perf_counter() - start)
    return response

# Class-based (built-in)
from fastapi.middleware.cors import CORSMiddleware
app.add_middleware(
    CORSMiddleware,
    allow_origins=["http://localhost:3000"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
```

Execution order: last added middleware is outermost (runs first on request, last on response). See [references/middleware.md](references/middleware.md) for full middleware reference including GZip, HTTPS redirect, TrustedHost, and custom ASGI middleware.

### Testing

```python
from fastapi.testclient import TestClient

client = TestClient(app)

def test_read_item():
    response = client.get("/items/1")
    assert response.status_code == 200
    assert response.json() == {"item_id": 1}

def test_create_item():
    response = client.post("/items/", json={"name": "Foo", "price": 42.0})
    assert response.status_code == 201
```

Override dependencies for isolation:

```python
app.dependency_overrides[get_db] = lambda: test_db_session
client = TestClient(app)
# ... tests ...
app.dependency_overrides = {}
```

Use `with TestClient(app) as client:` to trigger lifespan events. See [references/testing.md](references/testing.md) for WebSocket testing, async testing, and database test fixtures.

### Lifespan events

```python
from contextlib import asynccontextmanager

@asynccontextmanager
async def lifespan(app: FastAPI):
    # Startup: load models, init connections
    ml_models["v1"] = load_model()
    yield
    # Shutdown: cleanup
    ml_models.clear()

app = FastAPI(lifespan=lifespan)
```

`on_event("startup")` / `on_event("shutdown")` are deprecated. If `lifespan` is set, event handlers are NOT called.

### Settings

```python
from functools import lru_cache
from pydantic_settings import BaseSettings, SettingsConfigDict

class Settings(BaseSettings):
    app_name: str = "My API"
    database_url: str
    debug: bool = False
    model_config = SettingsConfigDict(env_file=".env")

@lru_cache
def get_settings():
    return Settings()
```

## Reference Documents

Detailed reference material organized by topic:

- **[API Reference](references/api_reference.md)**: FastAPI class, APIRouter, parameter functions (Query/Path/Header/Cookie/Body/Form/File), Response classes, exceptions, UploadFile, encoders, StaticFiles, Jinja2Templates, BackgroundTasks, WebSocket
- **[Middleware](references/middleware.md)**: Creating middleware (function/class/ASGI), execution order, CORSMiddleware, GZipMiddleware, HTTPSRedirectMiddleware, TrustedHostMiddleware, middleware vs dependencies comparison
- **[Advanced Patterns](references/advanced.md)**: Lifespan events, settings/env vars, custom responses (Streaming/File/ORJSON), additional status codes, response cookies/headers, dataclasses, behind a proxy, OpenAPI callbacks/webhooks, client generation
- **[Testing](references/testing.md)**: TestClient patterns, dependency overrides, WebSocket testing, lifespan testing, database test fixtures, async testing with httpx, project structure
- **[Security](references/security.md)**: OAuth2PasswordBearer, JWT tokens, password hashing (pwdlib/Argon2), OAuth2 scopes, HTTP Basic auth, API keys, OpenID Connect, timing attack prevention
- **[Database](references/database.md)**: SQLModel setup, model inheritance pattern (Base/Table/Public/Create/Update), session dependency, full CRUD operations, test fixtures with in-memory SQLite

---
> Source: [nealepetrillo/claude-skills-fastapi](https://github.com/nealepetrillo/claude-skills-fastapi) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
