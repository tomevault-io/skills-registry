---
name: fastapi-endpoint
description: Generate a complete FastAPI CRUD endpoint with Pydantic v2 schemas, async SQLAlchemy queries, dependency injection, response models, and error handling. Use when this capability is needed.
metadata:
  author: RoninForge
---

# Generate FastAPI Endpoint

## When to Use

Use this skill when creating a new API endpoint or resource with full CRUD operations.

## Instructions

1. Read the project to understand conventions: database setup (sync/async SQLAlchemy), existing schemas, router structure, auth pattern.

2. Generate these components:

   **Pydantic schemas** (in `schemas/`):
   - `Create` model (request body for POST)
   - `Update` model (request body for PATCH, all fields optional)
   - `Response` model (with `model_config = ConfigDict(from_attributes=True)`)
   - Use `Annotated[type, Field(...)]` for constraints (not `constr`/`conint`)
   - Use `@field_validator` (not `@validator`)
   - Explicit field list (never auto-generate from ORM)

   **Router** (in `routers/`):
   - Use `APIRouter` with `prefix` and `tags`
   - `response_model` on every endpoint
   - `status_code=201` on POST
   - Async endpoints with `await` for all DB operations
   - `selectinload`/`joinedload` for eager loading relationships
   - HTTPException for error cases
   - Dependency injection with `Annotated[type, Depends()]`

   **Include router in main app**

3. All database access must use the async session dependency pattern.
4. All endpoints must have `response_model` set.
5. Use `@field_validator` and `@model_validator` (Pydantic v2), never `@validator` or `@root_validator`.

---
> Source: [RoninForge/roninforge-fastapi](https://github.com/RoninForge/roninforge-fastapi) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
