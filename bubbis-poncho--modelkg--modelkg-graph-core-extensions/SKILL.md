---
name: modelkg-graph-core-extensions
description: Extend the Graph Core service in ModelKG (new endpoints, validators, and event emission). Use when adding graph CRUD behavior, templates, master nodes, suggestions, or when publishing graph.changed events. Use when this capability is needed.
metadata:
  author: bubbis-poncho
---

# ModelKG Graph Core Extensions

## Overview
This skill guides changes to Graph Core so new endpoints and behaviors stay consistent with storage, validation hooks, and eventing.

## Workflow

1) Choose the API area (`/api/graphs`, `/api/templates`, `/api/master-nodes`, `/api/suggestions`, etc.).
2) Add or update a router in `backend/services/graph-core/src/api`.
3) Wire the router in `src/main.py` and the container dependencies in `src/config.py`.
4) Apply validation hooks and publish events where needed.
5) Update docs and tests.

## Patterns to follow

- Use the service container (`src/config.py`) for dependencies.
- Prefer `ValidationHook` for ontology validation.
- Publish `graph.changed` via `src/infrastructure/events/graph_events.py` for state changes.
- Keep external routes under `/api/*` prefixes.

## References

- `references/graph-core-structure.md` for module locations.
- `references/eventing.md` for emitting `graph.changed`.
- `references/testing.md` for test placement and patterns.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bubbis-poncho) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
