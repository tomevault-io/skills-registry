---
name: api-contract
description: > Use when this capability is needed.
metadata:
  author: kevcofett
---

# API Contract Designer

## Commands

- `/api design` — Design new API endpoints from a description
- `/api validate` — Validate an existing OpenAPI spec for completeness
- `/api mock` — Generate mock request/response examples
- `/api diff` — Compare two API versions, highlight breaking changes

## Procedure

### Phase 1: Requirements

Ask the user for:

1. What resource/domain does this API serve?
2. What operations are needed (CRUD, search, bulk)?
3. Authentication method (API key, OAuth, JWT, none)?
4. Expected consumers (agents, external clients, Copilot Studio flows)?

### Phase 2: Contract Design

Generate an OpenAPI 3.0 spec with paths, request/response schemas, query parameters, auth security schemes, and example values for every field.

### Phase 3: Validation

| Check | Rule |
|-------|------|
| Naming | snake_case fields, kebab-case paths |
| Versioning | Path prefix /v1/, /v2/ |
| Pagination | Cursor-based preferred |
| Error format | RFC 7807 Problem Details |
| Idempotency | PUT and DELETE must be idempotent |
| Content type | application/json default |

### Phase 4: Output

- `contracts/{resource}.openapi.yaml` — OpenAPI spec
- `contracts/{resource}.examples.json` — Request/response examples

## MCMAP-Specific Patterns

- Use Dataverse Web API conventions (OData v4) for entity endpoints
- Connection references must be declared in solution customizations.xml
- Agent-to-agent contracts go in `contracts/INTER_AGENT_CONTRACT_v1.json`
- KB document endpoints must respect the 36K char limit

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kevcofett) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
