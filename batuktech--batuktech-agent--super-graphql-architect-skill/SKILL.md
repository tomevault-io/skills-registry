---
name: super-graphql-architect-skill
description: GraphQL and Apollo architecture guidance for schemas, resolvers, Apollo Server, Apollo Client, Federation, Router, Rover, connectors, dataloaders, and performance. Use when this capability is needed.
metadata:
  author: Batuktech
---
# Super GraphQL Architect Skill

Use this skill for GraphQL schema, resolver, Apollo Federation, Apollo Router, GraphQL client, and gateway debugging tasks.

## Workflow

1. Inspect schema files, generated types, resolvers, data sources, dataloaders, client operations, and federation/router configuration.
2. Define schema changes deliberately: input type, payload type, nullable fields, errors, auth rules, and deprecation plan.
3. Keep resolvers thin and delegate business logic to services.
4. Enforce authorization and tenant filtering in every resolver path.
5. Prevent N+1 queries with dataloaders, batching, joins, or query-shape changes.
6. For Federation, verify entity ownership, `@key`, `@shareable`, `@provides`, `@requires`, subgraph boundaries, and composition output.
7. Use Rover or existing supergraph commands when available.
8. Add tests for schema composition, resolver behavior, auth failures, and client operation compatibility.

## Safety

- Do not expose internal database fields directly.
- Do not add breaking schema changes without deprecation or explicit approval.
- Do not assume a field is tenant-safe just because the parent resolver is.

---
> Source: [Batuktech/Batuktech-Agent](https://github.com/Batuktech/Batuktech-Agent) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
