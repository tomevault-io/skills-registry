---
name: fastapi-expert
description: > Use when this capability is needed.
metadata:
  author: naKarthikSurya
---

# FastAPI Expert Skill

## Goal

Implement production-grade FastAPI backend features with Pydantic validation, async database
access, clean router organization, and full OpenAPI documentation.

## When to Use

- The project uses FastAPI as the backend framework.
- A new router, service, schema, model, or dependency must be implemented.
- An existing FastAPI feature has a bug, performance issue, or architecture violation.

## Project Structure Convention

```
app/
  api/
    v1/
      routers/
        <feature>.py
      dependencies/
        auth.py
        db.py
  core/
    config.py
    security.py
  models/
    <feature>.py       # SQLAlchemy ORM models
  schemas/
    <feature>.py       # Pydantic request/response models
  services/
    <feature>.py       # Business logic
  db/
    session.py
    migrations/        # Alembic migration files
  main.py
```

## Implementation Rules

### Routers
- Group routes by resource using `APIRouter(prefix="/resource", tags=["Resource"])`.
- Use `Depends()` for auth, database session, and shared logic — never inline.
- Return Pydantic response models, never raw dicts or ORM objects.

### Pydantic Schemas
- Separate schemas: `CreateSchema`, `UpdateSchema`, `ResponseSchema`.
- Use `model_config = ConfigDict(from_attributes=True)` for ORM compatibility.
- Use `Field(...)` with description and example for OpenAPI docs.

### Services
- All business logic in service layer functions.
- Accept a database session as a dependency parameter.
- Raise `HTTPException` with specific status codes and details.
- No direct route/request context in service logic.

### Database (SQLAlchemy + Alembic)
- Use async SQLAlchemy (`AsyncSession`) for all DB operations.
- Never use `session.commit()` inside services — let the route handler manage transactions.
- All schema changes via Alembic migrations. Never `Base.metadata.create_all()` in production.

### Dependencies
- Use `Depends()` for reusable logic: get current user, get DB session, check permissions.
- Type-annotate all dependencies for IDE and runtime clarity.

### Async
- All route handlers and service functions are `async def` unless truly synchronous.
- Use `asyncio.gather()` for parallel independent async calls.

## Review Checklist

- [ ] All routes return a typed Pydantic response model
- [ ] All business logic in service layer, not in route handlers
- [ ] Auth applied via `Depends()` on protected routes
- [ ] Alembic migration created for every schema change
- [ ] Pytest tests written for service functions and API endpoints

---
> Source: [naKarthikSurya/Talos](https://github.com/naKarthikSurya/Talos) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
