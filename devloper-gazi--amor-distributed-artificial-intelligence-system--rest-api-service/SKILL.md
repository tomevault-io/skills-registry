---
name: rest-api-service
description: Build a Python FastAPI REST service with CRUD routes, validation, and tests Use when this capability is needed.
metadata:
  author: devloper-gazi
---

# rest_api_service — ground rules

Multi-file output. Layout:

```
main.py              # FastAPI app + routes
models.py            # Pydantic schemas
storage.py           # in-memory or sqlite3 backend (no SQLAlchemy)
test_main.py         # pytest suite
requirements.txt     # fastapi + uvicorn + pytest + pytest-asyncio (pinned)
```

The Cycle D sandbox accepts an `additional_files` JSON metadata
field — use it to split files cleanly. If multi-file isn't an
option, fall through to a single `main.py` with everything inline.

## Route contract (per resource — example: items)

* `GET  /items` → list (paginated via `?skip=0&limit=50`)
* `GET  /items/{id}` → single (404 with descriptive detail)
* `POST /items` → create (201 Created, returns the created resource)
* `PUT  /items/{id}` → update (200 OK, full replace)
* `DELETE /items/{id}` → delete (204 No Content)

## Validation

* Pydantic v2 schemas. Constraints inline via `Field(...,
  min_length=..., max_length=..., ge=..., le=...)`.
* 422 errors keep FastAPI's default shape (loc + msg + type).
* Custom 4xx for business rules (e.g. duplicate name → 409 with
  `{detail: "name already exists"}`).

## Storage

* If the spec doesn't require persistence, use a top-level dict.
* If persistence is required, use stdlib `sqlite3` (no SQLAlchemy
  dependency).  Connection per-request via `Depends`.

## Tests

* `from fastapi.testclient import TestClient`.
* Happy path: create, list, get, update, delete — assert each
  status code + each response body field.
* Error paths: 404 on missing id, 422 on bad input, 409 on
  duplicate (if applicable).
* Use pytest fixtures for the client + reset state between tests.

## Anti-patterns

* DON'T use SQLAlchemy unless the user asks for it.
* DON'T return raw model instances — always go through a
  response_model so the OpenAPI schema is correct.
* DON'T `print()` in handlers — use `logging` (FastAPI sets up the
  root logger).
* DON'T forget `from __future__ import annotations` if you use
  forward type refs.

---
> Source: [devloper-gazi/Amor-Distributed-Artificial-Intelligence-System](https://github.com/devloper-gazi/Amor-Distributed-Artificial-Intelligence-System) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
