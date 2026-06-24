---
name: fastapi-validate
description: Scan a FastAPI project for Pydantic v1 leftovers, async anti-patterns, missing response models, database session issues, and security gaps. Use when this capability is needed.
metadata:
  author: RoninForge
---

# Validate FastAPI Project

## When to Use

Use this skill when auditing a FastAPI project for quality, performance, or security issues.

## Instructions

1. Check for Pydantic v1 patterns:
   - `.dict()`, `.json()`, `.parse_obj()`, `.parse_raw()`, `.__fields__`
   - `@validator`, `@root_validator`
   - `class Config:` with `orm_mode`
   - `conint`, `constr`, `confloat` constrained types
   - `from pydantic import BaseSettings` (moved to `pydantic-settings`)

2. Check for async anti-patterns:
   - `requests.*` in `async def` endpoints
   - `time.sleep()` in `async def` endpoints
   - `open()` file I/O in `async def` endpoints
   - Sync database drivers (psycopg2, PyMySQL) with async engine
   - Missing `await` on async calls
   - CPU-heavy code without `run_in_executor`

3. Check for missing response models:
   - Endpoints without `response_model` parameter
   - Response models that expose sensitive fields (password, hashed_password, secret_key)

4. Check for database issues:
   - Sessions not using dependency injection (manual creation without cleanup)
   - Missing `expire_on_commit=False` on async sessions
   - Lazy loading without eager load options (causes N+1 or MissingGreenlet)
   - Missing `pool_pre_ping=True` on engine

5. Check for security issues:
   - Hardcoded secrets
   - Missing authentication on protected endpoints
   - CORS with `allow_origins=["*"]` in production
   - `Exception` caught and details exposed to client
   - File uploads without size/type validation
   - Missing rate limiting on public endpoints

6. Check for deprecated patterns:
   - `@app.on_event("startup"/"shutdown")` (use lifespan)
   - Inline `Depends()` instead of `Annotated`

7. Produce a summary report with issue count, severity, file locations, and fixes.

---
> Source: [RoninForge/roninforge-fastapi](https://github.com/RoninForge/roninforge-fastapi) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
