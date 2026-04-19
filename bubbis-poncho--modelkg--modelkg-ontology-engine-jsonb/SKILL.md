---
name: modelkg-ontology-engine-jsonb
description: Extend the ModelKG Ontology Engine with JSONB-backed models and async SQLAlchemy patterns. Use when adding or modifying metamodel, trait, constraint, or compute endpoints under /api/v1 in backend/services/ontology-engine. Use when this capability is needed.
metadata:
  author: bubbis-poncho
---

# ModelKG Ontology Engine (JSONB)

## Overview
This skill guides additions to the Ontology Engine using async SQLAlchemy and JSONB columns for flexible schemas.

## Workflow

1) Decide the API change (route, payload, and response).
2) Update or add SQLAlchemy models using JSONB.
3) Update repository/service logic.
4) Add or update routes in `src/api/routes`.
5) Ensure `/api/v1` routes and health endpoints stay intact.
6) Add tests and update OpenAPI if external consumers need the spec.

## Patterns to follow

- Use async SQLAlchemy (`create_async_engine`, `AsyncSession`).
- Prefer JSONB columns for schema-flexible payloads.
- Keep routes under `/api/v1` and let the gateway rewrite to `/api/*`.
- Keep settings in `src/config.py` (`DATABASE_URL` must use `postgresql+asyncpg`).

## References

- `references/ontology-structure.md` for module layout.
- `references/jsonb-patterns.md` for JSONB modeling guidance.
- `references/routes-and-contracts.md` for API conventions.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bubbis-poncho) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
