---
name: cross-layer-collaboration
description: Use when changes span frontend, backend, and tests where each layer needs a dedicated owner plus an integration agent for cross-cutting consistency
metadata:
  author: labrinyang
---

# Cross-Layer Collaboration: Full-Stack Team Pattern

## Overview

Changes that span frontend, backend, and tests need layer specialists who each own their domain, plus an integrator who ensures cross-cutting consistency.

**Core principle:** Conway's Law deliberately applied — one agent per layer produces clean boundaries. Integrator prevents Conway's downside (boundaries becoming walls).

**Management theory:** Conway's Law (structure → architecture), T-Shaped Skills (deep in one layer, understand all), Belbin Teamworker (integrator bridges gaps).

## When to Use

- Feature touches frontend + backend (+ tests)
- API contract between layers needs coordination
- Each layer has enough work for a dedicated agent
- Cross-cutting concerns (auth, error handling, logging) need consistency

**Don't use when:**
- Change is backend-only or frontend-only
- Small change that one agent can handle across layers
- Layers are completely decoupled (e.g., separate repos)

## Team Composition

```
coordinator (lead, delegate mode)
├── implementer (frontend)    — UI components, state, UX
├── implementer (backend)     — API, database, business logic
├── finisher (tests)          — Test coverage for both layers
└── integrator × 1            — API contracts, cross-cutting concerns
```

**Belbin coverage:**
- Thinking: integrator (Monitor-Evaluator for consistency)
- Action: FE implementer + BE implementer + finisher
- People: coordinator (Coordinator) + integrator (Teamworker)

**Optional additions:**
- `architect` — if the API contract is complex or new
- `reviewer` — if code quality review is needed post-integration
- `devil-advocate` — if the design has risky trade-offs

## The Process

### Phase 1: Forming — API Contract First

**Before any implementation:**
1. Coordinator (or architect) defines the API contract
2. Request/response shapes, error codes, auth requirements
3. Both FE and BE implementers review and agree

**Contract definition:**
```
Endpoint: POST /api/feature-x
Request:  { field1: string, field2: number }
Response: { id: string, status: "created" | "error" }
Errors:   { 400: validation, 401: unauth, 500: server }
Auth:     Bearer token required
```

**Critical:** The contract is the integration point. It MUST be agreed upon before parallel work begins. Changes to the contract during implementation require all parties to stop and re-agree.

### Phase 2: Storming — Contract Review

- Integrator reviews contract for completeness
- FE implementer flags: "I need pagination" → contract updated
- BE implementer flags: "This requires a new DB table" → noted
- Devil-advocate (if present): "What happens when the token expires mid-request?"

### Phase 3: Performing — Parallel by Layer

**Frontend implementer:**
- Builds UI components
- Implements state management
- Uses mock API matching the contract
- Writes frontend unit tests

**Backend implementer:**
- Builds API endpoints matching the contract
- Implements business logic + database
- Writes API tests (unit + integration)
- Ensures contract compliance

**Finisher (tests):**
- Writes end-to-end tests
- Writes integration tests that hit real API
- Validates both layers together
- Covers edge cases, error states

**Integrator:**
- Monitors both layers for contract drift
- Ensures consistent error handling across layers
- Checks cross-cutting concerns (auth, logging, validation)
- Resolves any API mismatches early

### Phase 4: Integration

Integrator leads:
1. Remove frontend mocks, connect to real API
2. Run integration tests
3. Verify error handling flows end-to-end
4. Check: auth, logging, validation consistent across layers

**Common integration issues:**
- Field naming mismatch (`userId` vs `user_id`)
- Error format inconsistency
- Missing CORS headers
- Auth token handling differences

### Phase 5: Adjourning — Review & Reflect

- Full code review across all layers
- E2E tests passing
- team-orchestrator:session-reflection captures learnings

## File Ownership Matrix

| Area | Owner | Other agents |
|------|-------|-------------|
| `src/components/`, `src/pages/` | FE implementer | NO edits |
| `src/api/`, `src/models/` | BE implementer | NO edits |
| `tests/e2e/`, `tests/integration/` | Finisher | NO edits |
| `src/types/`, `src/shared/` | Integrator | Read-only for others |
| API contract doc | Coordinator | All review, only coord edits |

**Rule:** No two agents edit the same file. Violations cause merge conflicts and wasted work.

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Starting implementation before contract agreed | Phase 1 is mandatory |
| FE and BE disagree on field names | Contract must specify exact names |
| No integration phase — just merge | Integrator role exists for this |
| Testing only happy paths | Finisher must cover error states |
| Contract changes mid-implementation | Stop all work, re-agree, then resume |
| FE uses mock that doesn't match contract | Mock must be generated from contract |

## Integration

**Pre-requisite:** team-orchestrator:orchestrating-work routes here
**Post-requisite:** team-orchestrator:session-reflection records learnings
**Related:** team-orchestrator:feature-development for module-level splitting

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/labrinyang) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
