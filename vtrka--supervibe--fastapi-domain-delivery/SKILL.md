---
name: fastapi-domain-delivery
description: Use WHEN delivering production FastAPI behavior TO apply Pydantic contracts, dependency injection, lifespan, async boundaries, SQLAlchemy sessions, background tasks, OpenAPI, and auth scopes safely.
metadata:
  author: vTRKA
---

# FastAPI Domain Delivery

## Overview

FastAPI Domain Delivery turns a FastAPI API change into a typed, async-correct, observable delivery plan. It centers Pydantic contracts, dependency injection, lifespan resources, async boundaries, SQLAlchemy session management, background tasks, generated OpenAPI, auth scopes, and testable rollback.

FastAPI's power is that runtime behavior and documentation can come from the same typed boundary. This skill keeps that boundary honest: no untyped dict contracts, no sync blocking hidden in async routes, no session leakage, and no background side effect without operational ownership.

## Expert Operating Standard

Follow `<resolved-supervibe-plugin-root>/docs/references/skill-expert-operating-standard.md`: read local source first, preserve evidence, follow FastAPI conventions, keep graph execution fast with scoped verification, and cap confidence when stack-specific runtime proof, rollback, or ownership is missing.

## When to Use

Use when adding or changing FastAPI routers, path operations, dependencies, Pydantic models, SQLAlchemy sessions, lifespan hooks, auth scopes, background tasks, OpenAPI metadata, or pytest coverage. Use for architecture review when the dependency graph, event loop, or schema contract is at risk.

Do not use for pure packaging, docs, or formatting changes with no API behavior.

## Step 0 - Read source of truth

1. Read app factory/bootstrap, router registration, dependency providers, settings, lifespan hooks, exception handlers, auth dependencies, SQLAlchemy session setup, and nearest tests.
2. Search memory/code for local Pydantic v2 patterns, response models, pagination, error envelope, auth scope naming, session dependency, and async test fixtures.
3. Identify sync vs async stack: database driver, HTTP clients, cache clients, queue clients, and file IO.
4. Check existing OpenAPI customization and whether generated schema is a contract consumed by clients.
5. Name the targeted pytest command that proves the route and dependency behavior.

## When not to use

- Do not use when the task is generic planning, product shaping, design, or release governance and no FastAPI implementation or review boundary exists.
- Do not use when another stack, database, security, deployment, or API owner has the primary decision; hand off to that specialist and keep the FastAPI part scoped.
- Do not use to justify dependency swaps, broad rewrites, heavy test runs during graph execution, or mixed old-plan scope without explicit approval.

## Decision tree

```text
Will the route perform IO?
  YES -> use async clients or explicitly offload blocking work outside the event loop.
  NO  -> keep route CPU-light or move CPU-heavy work to worker/background infrastructure.

Does it need request-scoped resources?
  YES -> model them as dependencies with yield cleanup where needed.
  NO  -> avoid globals unless immutable settings or app-lifespan resources.

Does it write to the database?
  YES -> session and transaction boundary must be explicit; background task cannot reuse request session.
  NO  -> use projection, limits, and response_model to keep output stable.

Does auth require scopes/tenant checks?
  YES -> declare scopes in dependency/OpenAPI and enforce tenant/domain authorization in service.
```

## Procedure

1. Define separate Pydantic models for create/update/request, response, and internal domain needs. Use field constraints, discriminators, aliases, and examples deliberately; avoid sharing persistence models as API contracts.
2. Register routes in the existing router/module structure. Keep path operations thin: dependencies, DTOs, service call, response mapping.
3. Build the dependency tree explicitly: settings, clients, session, current user, scopes, tenant, service. Use `Depends` rather than module-level hidden state.
4. Manage lifespan resources in FastAPI lifespan hooks or the app factory. Create engines/clients at app scope, sessions per request, and close resources on shutdown.
5. Preserve async boundaries. Use async SQLAlchemy sessions with async drivers in async routes; offload legacy sync calls with an explicit justification and bounded executor.
6. Put transaction ownership in service/repository conventions. Commit once per use case, rollback on typed exceptions, and never pass request sessions into background tasks after the response.
7. Treat background tasks as best-effort in-process work only. For durable jobs, use the local queue worker. Capture idempotency, retry, failure logging, and cancellation behavior.
8. Declare auth scopes and security schemes so OpenAPI matches enforcement. Tenant/domain authorization belongs in dependencies or services, not only in docs.
9. Preserve generated OpenAPI: operation ids, tags, response models, error responses, auth scopes, examples, and deprecation markers when relevant.
10. Instrument with structured logs, trace spans, metrics, request id, route, status, error code, user/tenant fields when safe, and dependency latency for slow IO.
11. Define rollback: route flag/removal, schema compatibility, Alembic downgrade or compensation, dependency config revert, background job drain/disable.

1. Read the source artifact, owned file paths, graph/task scope, and current project convention; record the evidence path, command, receipt, or runtime state that proves the starting point.
2. If required source, owner, dependency, runtime boundary, or approval is missing, stop and return BLOCKED with the missing field, impacted artifact, and next action instead of guessing.
3. After edits or reviewer findings, repair the smallest changed slice, rerun the same scoped command, and record command, exit code, pass/fail status, artifact path, confidence, and remaining blocker before completion.

## Worked example

Requirement: add `POST /projects/{project_id}/exports`.

Good delivery: `CreateExportRequest` validates format and filters; route depends on `get_current_user` with `exports:write`, `get_async_session`, and `ExportService`. The service checks project membership, creates an export record in one transaction, queues durable work through a queue adapter, returns `ExportResponse`, and exposes OpenAPI responses for 202, 400, 401, 403, 404, and 409. Background generation uses a worker with its own session, not FastAPI `BackgroundTasks`, because exports must survive process restart.

Verification: pytest covers invalid format, missing scope, cross-tenant project, duplicate active export, success response schema, OpenAPI scope, and transaction rollback on queue enqueue failure.

## Good and bad delivery paths

Good delivery path: deliver through Pydantic request/response models, route
dependencies for user/session/service, async-safe session scope, service
membership checks, durable queue adapter, and OpenAPI response mapping.
Runtime-specific tests include pytest/httpx or TestClient dependency
resolution, auth scopes, validation, duplicate active export, generated
schema, async DB rollback, queue enqueue failure, and worker session
ownership. Rollback disables the route or flag, leaves compatible export
records, pauses or drains the queue, and reverts OpenAPI/client generation
when needed. Failure boundaries are invalid filters, missing scope,
cross-tenant access, async session leakage, queue failure, and worker restart.

Bad unsafe path: use FastAPI BackgroundTasks with a request-scoped session for
durable work, return untyped dicts, and verify only the happy 202 response.
That path has no runtime-specific tests for the changed stack surface, no
concrete rollback beyond hope or manual cleanup, and weak failure boundaries
for invalid filters, missing scope, cross-tenant access, async session
leakage, queue failure, and worker restart.

## Anti-example or Common rationalizations

- "Pydantic will validate enough if we use dicts." Untyped dicts erase OpenAPI and let invalid nested contracts through.
- "This one sync call is fine." One blocking call can stall the event loop under concurrency.
- "BackgroundTasks is a queue." It is in-process best-effort work; it is not durable across crashes or restarts.
- "The session dependency can be reused in a task after response." Request-scoped sessions are closed after dependency cleanup.

## Common rationalizations

- "It is just FastAPI, so the generic implementation pattern is enough" fails because lifecycle, runtime, data, and deployment constraints differ by stack.
- "A broad suite will prove it faster" fails in graph execution; use the declared scoped command and reserve broad validators for the final release gate.
- "We can clean up the architecture while here" fails unless that cleanup is in the accepted graph scope, has rollback, and has its own verification path.

## Red flags

- Same Pydantic model is used for create, update, response, and persistence.
- Async route calls `requests`, sync SQLAlchemy, blocking file IO, or CPU-heavy code directly.
- Dependency graph has hidden globals for request-scoped state.
- Background task mutates database with a request session.
- Auth scope appears in docs but not enforcement, or enforcement lacks OpenAPI scope.
- OpenAPI diff changes operation ids or response shapes accidentally.
- Tests call service only and skip dependency/auth/validation behavior.

## Checklist

- Pydantic contracts are separate, constrained, and reflected in OpenAPI.
- Dependency graph is explicit and acyclic.
- Lifespan resources and request resources have correct cleanup.
- Async boundaries and blocking calls are accounted for.
- SQLAlchemy session/transaction lifetime is explicit.
- Background work has durability and retry decision.
- Auth scopes and tenant/domain checks are enforced and documented.
- Targeted pytest verifies success, validation, auth, transaction, and schema.

## Failure modes

- The FastAPI specialist applies a generic pattern and misses framework-owned lifecycle, typing, permission, migration, cache, or deployment behavior.
- The worker mixes a new graph task with stale plan scope and creates a larger review surface than the MVP flow needs.
- The task closes with prose only: no source evidence, no scoped command or final-gate deferral, no rollback, and no next action for blockers.

## Output contract

- `surface`: router, path operation, Pydantic request/response, OpenAPI metadata.
- `dependencyGraph`: settings, clients, session, user/scopes, services, and cleanup points.
- `asyncPlan`: allowed async clients, blocked sync calls, offload decisions, cancellation behavior.
- `persistencePlan`: session lifetime, transaction owner, migration and rollback notes.
- `backgroundPlan`: in-process vs durable queue decision, idempotency, retry, failure handling.
- `securityPlan`: auth scopes, tenant/domain checks, error responses.
- `verificationCommands`: targeted pytest/schema commands and expected evidence.
- `residualRisk`: concurrency, schema, queue, migration, or provider uncertainty.

## Guard rails

- Do not implement FastAPI changes from a generic skill when a stack-specific owner, migration path, or runtime boundary is missing.
- Avoid broad rewrites, dependency swaps, large test suites, or speculative architecture changes during fast MVP execution.
- Reject work that skips source search, rollback naming, or scoped verification for the changed slice.
- Stop when local project conventions, version constraints, permissions, data ownership, or deployment target are unknown.

## Verification

Use targeted pytest with dependency overrides only where they preserve route behavior. Verify validation, auth scopes, tenant authorization, dependency cleanup, async-safe DB/session behavior, background side effects, OpenAPI schema, transaction rollback, and structured error response. Use OpenAPI diff or snapshot checks when generated clients consume the schema.

- If any scoped check fails or new evidence appears, repair the smallest changed slice, rerun the same scoped command, and record command, exit code, pass/fail status, blockers, and final-gate deferrals before claiming completion.

## Supporting references

- examples/delivery.md - worked FastAPI implementation example, anti-example, and verification fixture.
- domain-packs/fastapi.md - FastAPI practice pack with review matrix, rollback prompts, and MVP guard rails.
- evals/regression.json - Use when calibrating fastapi-domain-delivery trigger boundaries, happy-path/failure-path coverage, boundary rollback behavior, or resource-tree regressions.

## Related

- `supervibe:source-driven-development`
- `supervibe:test-strategy`
- `supervibe:error-envelope-design`
- `supervibe:auth-flow-design`
- `supervibe:verification`
## Supporting references

### Resource tree hardening

- `references/practice-pack.md` - Read when fastapi-domain-delivery needs deeper load rules, local evidence anchors, gotchas, or a final checklist.
- `scripts/self-check.mjs` - Run with `--check` before claiming the fastapi-domain-delivery resource tree is complete; add `--json` when machine-readable evidence is needed.
- `evals/regression.json` - Use when tuning fastapi-domain-delivery trigger boundaries or checking should-trigger and should-not-trigger prompts.
- `examples/workflow.md` - Load when a concrete fastapi-domain-delivery workflow example or anti-example would clarify the next action.
- `templates/output-contract.md` - Use when emitting agent-output so status, evidence, blockers, confidence, and nextAction stay consistent.

---
> Source: [vTRKA/supervibe](https://github.com/vTRKA/supervibe) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
