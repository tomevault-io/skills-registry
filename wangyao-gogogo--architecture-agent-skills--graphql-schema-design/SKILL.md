---
name: graphql-schema-design
description: Use when reviewing GraphQL schema design, resolver architecture, data loading strategy, and API gateway patterns.
metadata:
  author: WangYao-GoGoGo
---

# GraphQL Schema Design

## When To Use
- The main decision is about schema structure, resolver organization, N+1 prevention, or pagination strategy.
- Reviewing mutation design, subscription patterns, or authorization boundaries.

## Workflow
1. Identify schema structure — does the schema model business capabilities, not database tables?
2. Review resolver architecture — are resolvers thin, delegating to service layers?
3. Check data loading — is `DataLoader` used to batch and cache database queries?
4. Review pagination — is the `Connection` pattern used for list fields?
5. Check mutation design — do mutations follow the input-payload pattern?
6. Review authorization — are authorization checks applied at the field/resolver level?
7. Recommend the cleanest schema and resolver structure.

## Output Format
```markdown
GraphQL schema review:
- Schema structure:
- Resolver architecture:
- Data loading (DataLoader):
- Pagination:
- Mutation design:
- Authorization:
- Recommended change:
- Verification:
```

---
> Source: [WangYao-GoGoGo/architecture-agent-skills](https://github.com/WangYao-GoGoGo/architecture-agent-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
