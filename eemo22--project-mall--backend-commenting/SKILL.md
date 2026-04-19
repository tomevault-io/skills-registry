---
name: backend-commenting
description: Apply backend and data-layer comments that explain intent, invariants, authorization, transactions, and failure strategy. Use when editing Route Handlers, Server Actions, domain use-cases, repositories, or Prisma schema. Use when this capability is needed.
metadata:
  author: eemo22
---

# Backend Commenting

- Explain intent and invariants, not line-by-line mechanics.
- Document input expectations, auth rules, and error strategy at each write endpoint.
- Document transaction boundaries and why atomicity is required.
- Add Prisma model comments for relationships and constraints.
- Keep comments short, accurate, and updated with code changes.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eemo22) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
