---
name: trip-api
description: | Use when this capability is needed.
metadata:
  author: osherkoren
---

# Trip Assistant API Development

This is a shortcut skill that loads the full trip-api skill.

For complete documentation, see:
- [trip-api/SKILL.md](trip-api/SKILL.md) - FastAPI development patterns
- [review-fastapi/SKILL.md](review-fastapi/SKILL.md) - Code review for FastAPI

## Quick Reference

### Architecture
```
API Gateway → Lambda → FastAPI (Mangum) → Agent
```

### Key Files
- `app/main.py` - FastAPI app and routes
- `app/schemas.py` - Pydantic request/response models
- `app/dependencies.py` - Dependency injection (agent)
- `app/handler.py` - Lambda handler (Mangum adapter)

### Development
```bash
# Run locally
fastapi dev app/main.py

# Run tests
pytest tests/ -v

# Run integration tests (requires OPENAI_API_KEY)
pytest tests/ -v -m integration
```

### Common Tasks
- **Add endpoint**: Define schema → Add route → Write tests
- **Test endpoint**: Use TestClient with mocked dependencies
- **Review code**: Use `/review-fastapi` skill
- **Fix failing tests**: Check dependency overrides in conftest.py

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/osherkoren) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
