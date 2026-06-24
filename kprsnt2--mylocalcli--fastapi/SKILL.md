---
name: fastapi
description: FastAPI framework best practices including Pydantic schemas, dependency injection, and async patterns. Use when this capability is needed.
metadata:
  author: kprsnt2
---

# FastAPI Best Practices

## Project Structure
- app/main.py - Application entry
- app/routers/ - Route handlers
- app/models/ - SQLAlchemy models
- app/schemas/ - Pydantic schemas
- app/services/ - Business logic
- app/dependencies.py - Dependency injection

## Pydantic Schemas
- Use separate schemas for input/output
- Use Field() for validation
- Use model_validator for complex validation
- Use Config for schema settings

## Dependency Injection
- Use Depends() for dependencies
- Create reusable dependencies
- Use yield for cleanup logic
- Cache expensive dependencies

## Async
- Use async def for I/O operations
- Use httpx for async HTTP
- Use databases/SQLAlchemy async
- Avoid blocking in async context

## Performance
- Use response_model for serialization
- Enable response caching
- Use background tasks
- Stream large responses
- Use connection pooling

---
> Source: [kprsnt2/MyLocalCLI](https://github.com/kprsnt2/MyLocalCLI) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
