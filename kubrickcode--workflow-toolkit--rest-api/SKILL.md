---
name: rest-api
description: | Use when this capability is needed.
metadata:
  author: kubrickcode
---

# REST API Design Standards

## Naming Conventions

### Field Naming

- Boolean: Require `is/has/can` prefix
- Date: Require `~At` suffix
- Use consistent terminology throughout the project (unify on either "create" or "add")

## Date Format

- ISO 8601 UTC
- Use DateTime type

## Pagination

### Cursor-Based (Industry Standard)

- Parameters: `?cursor=xyz&limit=20`
- Response: `{ data: [...], nextCursor: "abc", hasNext: true }`

## Sorting

- `?sortBy=createdAt&sortOrder=desc`
- Support multiple sort
- Specify defaults

## Filtering

- Range: `{ min, max }` or `{ gte, lte }`
- Complex conditions use nested objects

## URL Structure

### Nested Resources

- Maximum 2 levels

### Actions

- Allow verbs only when unable to represent as resource
- `/users/:id/activate`

## Response

### List

- `data` + pagination info

### Creation

- 201 + resource (excluding sensitive information)

### Error (RFC 7807 ProblemDetail)

- Required: `type`, `title`, `status`, `detail`, `instance`
- Optional: `errors` array

## Batch

- `/batch` suffix
- Success/failure count + results

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kubrickcode) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
