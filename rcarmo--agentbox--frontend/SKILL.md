---
name: frontend-bundling-via-bunnode
description: Bundling via Bun/Node with Make targets for typecheck and bundling Use when this capability is needed.
metadata:
  author: rcarmo
---

# Skill: Frontend bundling via Bun/Node

## Goal
Provide optional Make targets for TypeScript typecheck + bundling (Bun preferred).

## Recommended targets
- `node_modules` / `make deps-frontend` to install deps
- `make typecheck`
- `make build` (typecheck + bundle)
- `make build-fast` (bundle only)
- `make bundle-watch`
- `make bundle-clean`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rcarmo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
