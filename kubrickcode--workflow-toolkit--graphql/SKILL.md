---
name: graphql
description: | Use when this capability is needed.
metadata:
  author: kubrickcode
---

# GraphQL API Standards

## Naming Conventions

### Field Naming

- Boolean: Require `is/has/can` prefix
- Date: Require `~At` suffix
- Use consistent terminology throughout the project (unify on either "create" or "add")

## Date Format

- ISO 8601 UTC
- Use DateTime type

## Pagination

### Relay Connection Specification

```graphql
type UserConnection {
  edges: [UserEdge!]!
  pageInfo: PageInfo!
}

type UserEdge {
  node: User!
  cursor: String!
}

type PageInfo {
  hasNextPage: Boolean!
  endCursor: String
}
```

- Parameters: `first`, `after`

## Sorting

- `orderBy: [{ field: "createdAt", order: DESC }]`

## Type Naming

- Input: `{Verb}{Type}Input`
- Connection: `{Type}Connection`
- Edge: `{Type}Edge`

## Input

- Separate creation and modification (required for creation, optional for modification)
- Avoid nesting - IDs only

## Errors

### extensions (default)

- `code`, `field` in `errors[].extensions`

### Union (type safety)

- `User | ValidationError`

## N+1

- DataLoader is mandatory

## Documentation

- `"""description"""` is required
- Explicitly state Input constraints

## Deprecation

- `@deprecated(reason: "...")`
- Never delete types

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kubrickcode) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
