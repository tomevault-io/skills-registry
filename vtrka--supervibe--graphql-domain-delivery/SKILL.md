---
name: graphql-domain-delivery
description: Use WHEN implementing or reviewing GraphQL work involving schema contracts, resolvers, DataLoader, authorization, pagination, complexity/depth limits, federation, versioning, deprecation, testing, rollback, and observability. TO deliver stack-specific production changes with framework-native practices, verification evidence, and rollback discipline.
metadata:
  author: vTRKA
---

# GraphQL Domain Delivery

## Overview

GraphQL Domain Delivery protects the schema as a client contract while making
resolver implementation safe. It covers SDL or code-first contracts, resolver
boundaries, N+1 prevention with DataLoader, authorization, pagination,
complexity/depth limits, persisted queries, federation, versioning, deprecation,
testing, rollback, and observability across Apollo, Hot Chocolate, Strawberry,
gqlgen, and similar runtimes.

## Expert Operating Standard

Follow `<resolved-supervibe-plugin-root>/docs/references/skill-expert-operating-standard.md`: read local source first, preserve evidence, follow GraphQL conventions, keep graph execution fast with scoped verification, and cap confidence when stack-specific runtime proof, rollback, or ownership is missing.

## When to Use

Use for GraphQL schema additions, field changes, mutations, resolver work,
DataLoader coverage, auth checks, pagination, error contracts, query complexity
and depth limits, persisted queries, federation composition, subscriptions,
versioning, deprecation, schema diff checks, and client compatibility reviews.

## Step 0 - Treat SDL as the contract

1. Read the request, active instructions, owning agent, and allowed write set.
2. Locate SDL or generated schema, resolver files, DataLoader registry, auth
   middleware/directives, persisted-query manifest, schema CI, and federation
   config.
3. Search memory and code for touched types, fields, operations, clients,
   persisted query hashes, resolver call sites, loader usage, and deprecations.
4. Identify whether the change is additive, deprecating, breaking, federated,
   subscription-related, or resolver-only.
5. Map the data path behind each resolver so database/API work is delegated to
   the owning backend or data-store specialist when needed.

## When not to use

- Do not use when the task is generic planning, product shaping, design, or release governance and no GraphQL implementation or review boundary exists.
- Do not use when another stack, database, security, deployment, or API owner has the primary decision; hand off to that specialist and keep the GraphQL part scoped.
- Do not use to justify dependency swaps, broad rewrites, heavy test runs during graph execution, or mixed old-plan scope without explicit approval.

## Decision tree

```text
New field? -> additive SDL, honest nullability, auth policy, resolver owner, tests, persisted-query impact.
Rename/remove? -> breaking; deprecate with sunset, client usage scan, migration plan, rollback.
List field? -> Connection/cursor pagination unless bounded and justified; DataLoader for children.
Nested resolver? -> request-scoped DataLoader, batch key design, cache scope, auth per object.
Mutation? -> one cohesive side effect, idempotency key when retryable, typed result/error union.
Auth-sensitive field? -> field/object policy, deny-by-default, no hidden data in null/error.
Untrusted queries? -> persisted queries, complexity/depth/alias limits, timeout and cost telemetry.
Federated type? -> owner, @key, composition check, entity resolver, versioning across subgraphs.
Rollback? -> additive feature flag, alias/old field retained, gateway publish rollback, client compatibility.
```

## Procedure

1. Define the schema contract: operation, type/field names, nullability,
   arguments, pagination, errors, auth policy, clients, federation ownership,
   and compatibility promise.
2. Shape SDL deliberately. Additive fields are preferred; nullability must
   reflect business absence, not implementation uncertainty; enums are stable;
   input types are separated from output types; mutation payloads are explicit.
3. Design resolver boundaries. Resolver orchestrates context, auth, DataLoader,
   and service calls; domain rules live in services; database query shape lives
   behind repositories or stack specialists.
4. Prevent N+1. Every parent-to-children or entity reference resolver uses a
   request-scoped DataLoader with stable keys, batch ordering, per-request cache,
   auth-aware loading, and tests for batch count.
5. Enforce authorization. Check object ownership and field-level sensitivity,
   avoid leaking existence through errors, and keep admin/internal bypasses
   behind separate scopes or gateways.
6. Choose pagination. Use Relay Connection for unbounded lists, opaque cursors,
   deterministic ordering, `first/after` and optionally `last/before`, and
   bounded maximum page size. Offset pagination needs explicit bounded use.
7. Bound query cost. Enforce depth, complexity, alias count, directive count,
   timeout, maximum page size, and persisted-query policy for production.
8. Manage federation/versioning. Each field has an owner, composition is checked
   before publish, entity resolvers are DataLoader-backed, and deprecations carry
   reasons, sunset dates, and client migration tracking.
9. Test the contract. Include SDL validation, schema diff, resolver success and
   authorization denial, N+1 batch count, pagination edges, complexity rejection,
   federation composition, and persisted-query compatibility where relevant.
10. Name rollback: schema publish revert, feature flag, old field retained,
    deprecation delay, gateway composition rollback, resolver fallback, and
    persisted-query manifest rollback.

1. Read the source artifact, owned file paths, graph/task scope, and current project convention; record the evidence path, command, receipt, or runtime state that proves the starting point.
2. If required source, owner, dependency, runtime boundary, or approval is missing, stop and return BLOCKED with the missing field, impacted artifact, and next action instead of guessing.
3. After edits or reviewer findings, repair the smallest changed slice, rerun the same scoped command, and record command, exit code, pass/fail status, artifact path, confidence, and remaining blocker before completion.

## Worked example

Add `User.orders(first:, after:)`:

1. SDL adds `orders(first: Int!, after: String): OrderConnection!` with max page
   size enforced by validation.
2. Resolver checks viewer access to the `User`, then calls a request-scoped
   `ordersByUserLoader` keyed by `{userId, pageArgs}` or a service optimized for
   batched user IDs.
3. Cursor uses deterministic `(createdAt, id)` ordering and opaque encoding.
4. Tests cover authorized user, unauthorized user, empty page, next page,
   invalid cursor, max page size rejection, and one batched data call for N
   users.
5. Rollback removes the resolver from routing or hides the field behind a
   feature flag while keeping SDL additive until clients are stable.

## Good and bad delivery paths

Good delivery path: deliver additively in SDL, enforce pagination limits,
check viewer access in the resolver, batch data access with the local
loader/service pattern, and preserve persisted-query compatibility.
Runtime-specific tests include GraphQL operation tests for auth denial,
invalid cursor, max page size, empty/success pages, N+1 or batched call count,
gateway/composition where used, and persisted-query manifest impact. Rollback
hides the resolver behind a flag or alias, keeps SDL additive until clients
are stable, and reverts gateway/schema publish or manifest changes. Failure
boundaries are unauthorized object access, resolver batching bugs, cursor
corruption, schema publish failure, manifest drift, and client compatibility
break.

Bad unsafe path: remove or rename fields in place, fetch child rows per parent
without batching, skip persisted-query review, and verify with one playground
query. That path has no runtime-specific tests for the changed stack surface,
no concrete rollback beyond hope or manual cleanup, and weak failure
boundaries for unauthorized object access, resolver batching bugs, cursor
corruption, schema publish failure, manifest drift, and client compatibility
break.

## Anti-example or Common rationalizations

- "It is only one nested field" becomes N+1 as soon as the parent is queried in
  a list.
- "Nullable is safer" hides server errors as nulls and makes client contracts
  dishonest.
- "We can remove the old field now" is breaking unless client usage and sunset
  have been proven.
- "Internal graph does not need limits" ignores ad-hoc query amplification and
  compromised/internal clients.

## Common rationalizations

- "It is just GraphQL, so the generic implementation pattern is enough" fails because lifecycle, runtime, data, and deployment constraints differ by stack.
- "A broad suite will prove it faster" fails in graph execution; use the declared scoped command and reserve broad validators for the final release gate.
- "We can clean up the architecture while here" fails unless that cleanup is in the accepted graph scope, has rollback, and has its own verification path.

## Red flags

- Resolver returns `any`, `dynamic`, map-like objects, or untyped payloads.
- List resolver calls the database or service once per parent.
- Auth is checked at root resolver but not object/field level.
- Unbounded list field lacks cursor pagination and max page size.
- Production gateway accepts arbitrary queries despite persisted-query policy.
- Federation change lacks composition check.
- Deprecation reason lacks replacement and sunset date.
- Error handling leaks sensitive existence or backend exception details.

## Checklist

- Schema change is classified as additive, deprecation, or breaking.
- Nullability, arguments, pagination, auth, and error contract are explicit.
- Resolvers use DataLoader or equivalent batching for parent/child paths.
- Complexity/depth/persisted-query controls protect production execution.
- Federation ownership and composition are verified where applicable.
- Tests cover success, denial, N+1, pagination, error, complexity, and schema diff.
- Rollback and client compatibility are named.

## Failure modes

- The GraphQL specialist applies a generic pattern and misses framework-owned lifecycle, typing, permission, migration, cache, or deployment behavior.
- The worker mixes a new graph task with stale plan scope and creates a larger review surface than the MVP flow needs.
- The task closes with prose only: no source evidence, no scoped command or final-gate deferral, no rollback, and no next action for blockers.

## Output contract

- `status`: PASS, PARTIAL, BLOCKED, or DEFERRED.
- `scope`: types, fields, operations, resolvers, loaders, clients, subgraphs.
- `schemaContract`: SDL diff, nullability, pagination, auth, errors, clients.
- `resolverPlan`: boundaries, DataLoader, batching, service/data-store handoff.
- `securityControls`: auth, field policy, persisted queries, complexity/depth.
- `federationVersioning`: ownership, composition, deprecation, sunset, rollout.
- `testPlan`: schema diff, resolver cases, N+1, pagination, auth, complexity.
- `rollback`: schema publish revert, flags, old fields, manifest/gateway revert.
- `observability`: operation names, resolver timing, loader batch size, error codes.
- `verification`: commands run or final-gate deferral.
- `confidence`: score with caps for missing client, auth, N+1, or composition proof.

## Guard rails

- Do not implement GraphQL changes from a generic skill when a stack-specific owner, migration path, or runtime boundary is missing.
- Avoid broad rewrites, dependency swaps, large test suites, or speculative architecture changes during fast MVP execution.
- Reject work that skips source search, rollback naming, or scoped verification for the changed slice.
- Stop when local project conventions, version constraints, permissions, data ownership, or deployment target are unknown.

## Verification

- Validate SDL or generated schema and inspect schema diff for breaking changes.
- Run federation composition when any subgraph-owned type changes.
- Verify DataLoader coverage and batch-count tests for nested/list resolvers.
- Verify auth denial, object ownership, field sensitivity, and error taxonomy.
- Verify pagination boundaries, cursor stability, max page size, and complexity
  rejection.
- Verify persisted-query manifest impact and rollback or final-gate deferral.
- Monitor operation latency, resolver timings, loader batch sizes, error codes,
  rejected-query counts, and downstream data-source pressure post-deploy.

- If any scoped check fails or new evidence appears, repair the smallest changed slice, rerun the same scoped command, and record command, exit code, pass/fail status, blockers, and final-gate deferrals before claiming completion.

## Supporting references

Use local support files through progressive disclosure; keep the main SKILL.md as the operating contract and load deeper resources only when the active task needs them:
- Read `references/practice-pack.md` when graphql-domain-delivery needs deeper practice guidance, source evidence anchors, risk checks, or a final checklist beyond the core procedure.
- Run `scripts/self-check.mjs --check --json` after editing this graphql-domain-delivery resource tree or before claiming deterministic support-file readiness; use `--help` for options and `--dry-run` for read-only preview semantics.
- Use `evals/regression.json` when calibrating graphql-domain-delivery trigger boundaries, happy-path/failure-path coverage, boundary rollback behavior, or resource-tree regressions.
- Open `examples/workflow.md` when a concrete graphql-domain-delivery workflow example is needed for sequencing, evidence selection, or anti-example comparison.
- Use `templates/output-contract.md` when emitting graphql-domain-delivery-report so status, evidence, confidence, blockers, risks, rollback, and nextAction stay consistent.

## Related

- `supervibe:source-driven-development`
- `supervibe:project-memory`
- `supervibe:code-search`
- `supervibe:api-and-interface-design`
- `supervibe:auth-flow-design`
- `supervibe:test-strategy`
- `supervibe:verification`
- `graphql-schema-designer`

---
> Source: [vTRKA/supervibe](https://github.com/vTRKA/supervibe) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
