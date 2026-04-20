---
name: frontend-bundling-via-bun
description: Bundling via Bun with Make targets for typecheck and bundling Use when this capability is needed.
metadata:
  author: rcarmo
---

# Skill: Frontend bundling via Bun

## Goal
Provide Make targets for TypeScript typecheck + bundling using Bun.

## Recommended targets
- `make install` — install deps
- `make typecheck` — TypeScript type checking
- `make build` — typecheck + bundle for production
- `make build-client` — bundle frontend only
- `make build-client-dev` — bundle frontend with sourcemaps
- `make vendor` — rebuild vendored frontend deps

## Vendoring
All frontend JS dependencies are vendored in `public/vendor/`.
The `make vendor` target rebuilds them from `node_modules/`.
Import maps in `index.html` resolve bare specifiers to vendored files.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rcarmo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
