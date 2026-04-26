---
name: fastapi-patterns
description: Dependencies, Pydantic, routers, and OpenAPI best practices. Use when this capability is needed.
metadata:
  author: sraloff
---

# FastAPI / Modern Python API

## When to use this skill
- Building high-performance APIs with FastAPI.
- Defining Pydantic models.
- Structuring API projects.

## 1. Structure
- **Routers**: Split routes into modules (`app/routers/users.py`) and include them in `main.py`.
- **Dependencies**: Use Dependency Injection (`Depends()`) for DB sessions, auth, and shared logic.

## 2. Pydantic Models
- **Separation**: Create separate models for Input (`UserCreate`), Output (`UserResponse`), and Database (`UserDB`).
- **Validation**: Use strict Types and `Field(...)` for validation metadata.

## 3. Async
- **Def vs Async Def**: Use `async def` for endpoints that await I/O (DB, HTTP). Use `def` only if blocking CPU.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sraloff) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
