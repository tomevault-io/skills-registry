---
name: enterprise-architecture
description: > Use when this capability is needed.
metadata:
  author: rsolipuram
---

# Enterprise Architecture Reasoning

Use this knowledge to interpret indexed API specs from the Enterprise Researcher knowledge base. Never treat these patterns as a replacement for reading actual specs.

## Knowledge Base Orientation

Read the knowledge base at `.enterprise-researcher/`, structured in tiers:

- **Tier 1** — `index/domain-map.md`: Read this FIRST for any architectural question. Provides the ecosystem overview.
- **Tier 1** — `index/service-graph.yaml`: Compact topology of ALL services. Contains entities, dependencies, patterns.
- **Tier 2** — `index/domains/<domain>.md`: Load when working on a specific domain.
- **Tier 3** — `index/services/<service>.md`: Load when reasoning about specific services. Includes structured frontmatter.
- **Tier 4** — `specs/<service>/openapi.json`: Load only for exact schema definitions or field-level detail.
- **Cross-cutting** — `index/dependencies.md` for dependency questions, `index/schemas.md` for data model questions, `index/operations.md` for endpoint discovery, `index/changelog.md` for recent changes.

Suggest `/sync` when the knowledge base is stale. Suggest `/setup` when `.enterprise-researcher/` does not exist.

## Integration Pattern Selection

Select the appropriate integration pattern using this decision heuristic:

- **Direct REST** — Caller needs immediate response; target is reliable. Check `dependencies.md` for existing chains — avoid deepening critical paths.
- **Event-Driven / Async** — Caller tolerates eventual consistency; need to decouple. Check `service-graph.yaml` `patterns` field for services already using `webhook-events` or `async`.
- **API Gateway / BFF** — Frontend aggregates from multiple services. Look for existing `*-bff` services in the registry before creating new ones.
- **Shared Database** — Flag as anti-pattern. Each service must own its data.

For full pattern details with diagrams, pros/cons, and resilience requirements, see `references/integration-patterns.md`.

## Dependency Impact Analysis

To analyze the impact of an API change:

1. Check **direct dependents** in `dependencies.md` — services that call the changing endpoint.
2. Trace **transitive dependents** in `service-graph.yaml` — services depending on direct dependents.
3. Search **schema consumers** in `schemas.md` — services referencing the changing schema.
4. Identify **event subscribers** — services consuming events from the changing service.

Rate impact severity:
- **CRITICAL**: Breaking change to a widely-used endpoint or schema
- **HIGH**: Change to an endpoint used by 3+ services
- **MEDIUM**: Change to an endpoint used by 1-2 services
- **LOW**: Additive change (new optional field, new endpoint)

Always show which specific services and endpoints were found and how the dependency was traced.

## API Design Evaluation

To evaluate an API design against ecosystem standards:

1. Check naming conventions against `schemas.md` — match existing field name patterns, enum styles, and ID formats.
2. Verify versioning approach against `service-graph.yaml` `api_version` fields for consistency.
3. Check entity ownership — does this service duplicate another service's data? Cross-reference `key_entities` in `service-graph.yaml`.
4. Verify auth method matches domain standard — compare against `auth` fields in `service-graph.yaml`.
5. Apply the full governance checklist from `references/api-governance-checklist.md`.

## Domain Boundary Analysis

To analyze domain boundaries for a potential service split or merge:

1. Read domain summaries at `index/domains/<domain>.md` for current boundaries.
2. Check entity ownership in `service-graph.yaml` — if two services both manage the same entity, they may belong together.
3. Check coupling — if two services always appear together in dependency chains, consider merging.
4. Check team boundaries — services owned by the same team with high interdependency are merge candidates.
5. Check for circular dependencies in `service-graph.yaml` — these often indicate incorrect domain boundaries.

## Migration Strategy

To plan a migration (monolith decomposition, API version upgrade, service consolidation):

1. Map the current state by loading `domain-map.md` and `service-graph.yaml`.
2. Identify the strangler fig boundary — find the component with fewest external consumers (from `operations.md`).
3. Check consumer count per endpoint being migrated — high-consumer endpoints migrate last.
4. Plan rollout phases: parallel run, gradual cutover, deprecation notice, removal.
5. Identify affected teams from `service-graph.yaml` team fields — ensure communication plan covers all stakeholders.
6. Recommend strangler fig for monolith decomposition. Recommend parallel run with feature flags for version migration.

## References

For detailed patterns and checklists, see:
- `references/integration-patterns.md` — Integration pattern details with diagrams and resilience requirements
- `references/api-governance-checklist.md` — API design review checklist grounded in ecosystem standards

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rsolipuram) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
