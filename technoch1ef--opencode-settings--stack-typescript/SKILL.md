---
name: stack-typescript
description: TypeScript/Node repo conventions, common scripts, and verification commands. Use when this capability is needed.
metadata:
  author: technoch1ef
---

## Detection
- `package.json` exists
- Package manager heuristics:
  - `pnpm-lock.yaml` => pnpm
  - `yarn.lock` => yarn
  - `package-lock.json` => npm
  - `bun.lockb` or `bun.lock` => bun

## Worker rules
- Do not run tests (leave for overseer)
- Prefer running formatters when available (e.g. `npm run format`)
- Keep changes minimal and aligned with existing patterns

## Overseer verification
- Use the repo's scripts first (inspect `package.json`)
- Typical commands (pick the ones that exist):
  - `npm run lint`
  - `npm run typecheck`
  - `npm test`
  - `npm run build`

## Common pitfalls
- Update types when changing runtime behavior
- Keep imports consistent with existing lint rules
- Avoid introducing new tooling unless requested

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/technoch1ef) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
