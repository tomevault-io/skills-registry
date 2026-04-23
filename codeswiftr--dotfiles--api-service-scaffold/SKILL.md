---
name: api-service-scaffold
description: Scaffold production-ready API services with proper structure, error handling, and best practices for FastAPI, Express, or Go. Use when this capability is needed.
metadata:
  author: codeswiftr
---

# API Service Scaffold

## When to Use
- Starting a new API service/microservice.
- Refactoring an existing API to best practices.
- Setting up a consistent project structure.

## Framework Templates

### FastAPI (Python)
```
src/
├── __init__.py
├── main.py              # App entry, middleware
├── config.py            # Settings with pydantic
├── dependencies.py      # Dependency injection
├── api/
│   ├── __init__.py
│   ├── routes/
│   │   ├── __init__.py
│   │   ├── health.py    # Health/readiness checks
│   │   └── v1/
│   │       ├── __init__.py
│   │       └── users.py
│   └── middleware/
│       └── error_handler.py
├── core/
│   ├── __init__.py
│   ├── exceptions.py    # Custom exceptions
│   └── security.py      # Auth helpers
├── models/
│   ├── __init__.py
│   └── user.py          # Pydantic models
├── services/
│   ├── __init__.py
│   └── user_service.py  # Business logic
└── db/
    ├── __init__.py
    ├── session.py       # DB connection
    └── repositories/
        └── user_repo.py # Data access
tests/
├── conftest.py
├── test_health.py
└── api/
    └── test_users.py
```

### Express (Node.js/TypeScript)
```
src/
├── index.ts             # Entry point
├── app.ts               # Express app setup
├── config/
│   └── index.ts         # Environment config
├── routes/
│   ├── index.ts         # Route aggregator
│   ├── health.ts
│   └── v1/
│       └── users.ts
├── middleware/
│   ├── errorHandler.ts
│   ├── auth.ts
│   └── validate.ts
├── controllers/
│   └── userController.ts
├── services/
│   └── userService.ts
├── models/
│   └── user.ts
├── repositories/
│   └── userRepository.ts
└── utils/
    └── logger.ts
tests/
├── setup.ts
└── routes/
    └── users.test.ts
```

## Essential Components

### Health Check Endpoint
```python
# FastAPI
@router.get("/health")
async def health():
    return {"status": "healthy", "timestamp": datetime.utcnow()}

@router.get("/ready")
async def ready(db: Session = Depends(get_db)):
    try:
        db.execute(text("SELECT 1"))
        return {"status": "ready", "database": "connected"}
    except Exception:
        raise HTTPException(503, "Database not ready")
```

### Error Handling
```python
# Custom exception handler
class AppException(Exception):
    def __init__(self, status_code: int, message: str, details: dict = None):
        self.status_code = status_code
        self.message = message
        self.details = details or {}

@app.exception_handler(AppException)
async def app_exception_handler(request, exc):
    return JSONResponse(
        status_code=exc.status_code,
        content={
            "error": exc.message,
            "details": exc.details,
            "request_id": request.state.request_id
        }
    )
```

### Request Validation
```python
from pydantic import BaseModel, Field, validator

class CreateUserRequest(BaseModel):
    email: str = Field(..., regex=r'^[\w\.-]+@[\w\.-]+\.\w+$')
    name: str = Field(..., min_length=1, max_length=100)

    @validator('email')
    def lowercase_email(cls, v):
        return v.lower()
```

### Structured Logging
```python
import structlog

logger = structlog.get_logger()

@app.middleware("http")
async def logging_middleware(request, call_next):
    request_id = str(uuid4())
    structlog.contextvars.bind_contextvars(request_id=request_id)

    logger.info("request_started", path=request.url.path, method=request.method)
    response = await call_next(request)
    logger.info("request_completed", status=response.status_code)

    return response
```

## Workflow
1. **Choose framework** based on team expertise.
2. **Create directory structure** following template.
3. **Set up configuration** with environment validation.
4. **Implement health endpoints** first.
5. **Add error handling middleware**.
6. **Create first route** with full request/response cycle.
7. **Add tests** for the route.
8. **Document API** (OpenAPI for FastAPI, Swagger for Express).

## Output Checklist
- [ ] Project structure created.
- [ ] Configuration with env validation.
- [ ] Health/readiness endpoints.
- [ ] Error handling middleware.
- [ ] Request validation.
- [ ] Structured logging.
- [ ] First test passing.
- [ ] API documentation accessible.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/codeswiftr) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
