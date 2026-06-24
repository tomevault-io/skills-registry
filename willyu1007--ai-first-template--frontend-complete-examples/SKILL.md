---
name: frontend-complete-examples
description: Example feature layout and end-to-end UI flow wiring. Keywords: frontend example, full example, feature folder, code sample. Use when this capability is needed.
metadata:
  author: willyu1007
---

# Complete Examples

This skill provides a small example showing a feature folder layout and how UI, hooks, and APIs connect.

---

## Example feature layout

```text
src/features/users/
  api/
    users_api.ts
  components/
    UsersPage.tsx
    UserRow.tsx
  hooks/
    use_users.ts
  types/
    user.ts
  index.ts
```

---

## Example flow

1. Route renders `UsersPage`
2. `UsersPage` uses a query hook (`use_users`) to load data
3. On mutation, invalidate/refetch affected queries
4. On error, show user-friendly message and capture exception to monitoring

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willyu1007) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
