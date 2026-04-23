---
name: api-contract
description: Define a typed API contract with validation and client helpers. Use when this capability is needed.
metadata:
  author: sinelanguage
---
# API Contract

Define request/response shapes, validation, and client usage for an API endpoint.

## When to Use

- Use this skill when adding or updating an API endpoint.
- Use this skill when you need typed contracts shared across client and server.

## Inputs

- Endpoint path and method
- Request body/query params
- Response shape and error cases
- Auth requirements

## Instructions

1. Define TypeScript types for request and response.
2. Add runtime validation (zod or equivalent).
3. Provide a typed client function signature.
4. Document error cases and status codes.

## Output

- Types, validation schema, and client function stub.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sinelanguage) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
