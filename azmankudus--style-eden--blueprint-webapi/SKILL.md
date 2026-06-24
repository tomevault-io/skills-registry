---
name: blueprint-webapi
description: > Use when this capability is needed.
metadata:
  author: azmankudus
---

# Web API Blueprint (Platform-Agnostic)

Design and architect robust HTTP REST APIs regardless of the underlying language or framework. This blueprint focuses entirely on **API Protocol, Contracts, and Governance**. It enforces a strict "Design-First / Contract-First" approach.

## Dependency Note
Other blueprints (like `blueprint-web-micronaut` or `blueprint-web-nodejs`) should inherit these rules to ensure any API built by a team complies with a unified standard.

## Command Reference

| Command | Phase | Description |
|---|---|---|
| `/blueprint init` | Pre | Start the discovery and planning session for the API |
| `/blueprint api-spec`| Pre | Scaffold an OpenAPI 3.1 specification document |
| `/blueprint check` | During | Validate project code against universally strict API contract standards |

---

## 1. Agent Anti-Hallucination Guardrails

Before finalizing any strategy or code generation for an API (regardless of language), the agent MUST verify against these strict rules:
*   **API Contract First**: Do not generate controllers, routers, or DTOs without an explicitly defined and agreed-upon OpenAPI contract. The contract is the master specification.
*   **No Auto-magic Endpoints**: Never invent or implement an endpoint that does not exist in the defined OpenAPI contract.
*   **RFC 7807 Errors**: Never hallucinate custom error structures. All HTTP errors MUST map exactly to RFC 7807 (Problem Details for HTTP APIs).
*   **Universal JSON Standards**: Always output standard JSON (`application/json`). Use `camelCase` for properties and `kebab-case` for paths.

---

## Reference Files

### Requirements & Governance
| File | Description |
|---|---|
| `references/requirement/api-contract.md` | API synchronization and strict governance (OpenAPI source of truth) |

### Design Standards
| File | Description |
|---|---|
| `references/design/openapi-standards.md` | Rules for URLs, Request/Response shapes, and standard headers |
| `references/design/schema-contract.md` | Strict envelope schemas (client/server) including auth and tracing headers |

### Testing
| File | Description |
|---|---|
| `references/testing/api-testing.md` | Guidelines for contract testing and schema validation |

---
> Source: [azmankudus/style-eden](https://github.com/azmankudus/style-eden) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
