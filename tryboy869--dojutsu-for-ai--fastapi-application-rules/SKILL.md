---
name: fastapi-application-rules
description: [Applies to: **/main.py] Specific guidelines for FastAPI application structure and setup in the main application file. Use when this capability is needed.
metadata:
  author: Tryboy869
---

- Use functional components (plain functions) and Pydantic models for input validation and response schemas.
- Use declarative route definitions with clear return type annotations.
- Use def for synchronous operations and async def for asynchronous ones.
- Minimize @app.on_event("startup") and @app.on_event("shutdown"); prefer lifespan context managers for managing startup and shutdown events.
- Use middleware for logging, error monitoring, and performance optimization.

---
> Source: [Tryboy869/dojutsu-for-ai](https://github.com/Tryboy869/dojutsu-for-ai) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
