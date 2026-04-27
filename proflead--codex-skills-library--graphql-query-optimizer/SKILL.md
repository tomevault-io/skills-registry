---
name: graphql-query-optimizer
description: Optimize GraphQL queries and resolvers for performance. Use when a mid-level developer needs to reduce N+1 or payload size. Use when this capability is needed.
metadata:
  author: proflead
---

# GraphQL Query Optimizer

## Purpose
Optimize GraphQL queries and resolvers for performance.

## Inputs to request
- Slow query or resolver traces.
- Schema and resolver stack.
- Performance targets or budgets.

## Workflow
1. Identify resolver hotspots and query depth.
2. Suggest batching, caching, or pagination.
3. Recommend schema or query adjustments.

## Output
- Optimization plan with example changes.

## Quality bar
- Prioritize fixes with measurable impact.
- Avoid breaking schema changes without migration.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/proflead) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
