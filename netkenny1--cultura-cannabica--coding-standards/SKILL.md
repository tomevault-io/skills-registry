---
name: coding-standards
description: Universal coding standards for JavaScript/TypeScript: naming, immutability, error handling, file size, and quality checklist. Use when writing or reviewing any project code. Use when this capability is needed.
metadata:
  author: netkenny1
---

# Coding Standards

Standards for JavaScript/TypeScript applicable across this project and future work.

## When to Apply

- Writing new code or modifying existing modules
- Reviewing PRs or doing self-review
- Refactoring or adding features

---

## Principles

- **Readability first**: Clear names, small functions, consistent style
- **KISS**: Simplest solution that works; avoid over-engineering
- **DRY**: Extract repeated logic into functions or shared modules
- **YAGNI**: Don’t add features or abstraction until needed

---

## Naming

- **Variables**: Descriptive, not single letters (e.g. `cartItems`, `isLoading`)
- **Functions**: Verb-noun (e.g. `fetchProduct`, `validateCheckout`, `formatPrice`)
- **Booleans**: `is`, `has`, `should` prefix (e.g. `isValid`, `hasDiscount`)
- **Constants**: UPPER_SNAKE for true constants; camelCase for config objects

---

## Immutability (Critical)

- Do not mutate existing objects or arrays in place
- Prefer spread and new arrays:

```javascript
// Good
const updated = { ...user, name: newName }
const next = [...items, newItem]
const filtered = items.filter(...)

// Bad
user.name = newName
items.push(newItem)
```

---

## Functions and Files

- Functions: small and focused; under ~50 lines when practical
- Files: single responsibility; under ~400 lines typical, ~800 max
- Organize by feature/domain (e.g. cart, product, checkout) rather than only by type (all "utils")

---

## Error Handling

- Handle errors explicitly; never swallow them
- User-facing: short, clear messages; server/logs: full context
- Async: use try/catch with async/await; consider Promise.all for independent work
- Validate at boundaries (input, API responses); fail fast with clear errors

---

## Input and Validation

- Validate all external input (forms, query params, API payloads)
- Use schema-based validation (e.g. Zod) when available
- No hardcoded secrets; use env vars or config

---

## Code Quality Checklist

Before considering work complete:

- [ ] Names are clear and consistent
- [ ] Functions are small and focused
- [ ] No unnecessary mutation
- [ ] Errors handled and logged appropriately
- [ ] No `console.log` left in production paths
- [ ] No hardcoded secrets or sensitive data

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/netkenny1) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
