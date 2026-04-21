---
name: testing-selection
description: Choose the smallest meaningful verification commands for a given change. Use when this capability is needed.
metadata:
  author: jelidia
---

Guide:
- Type changes -> `npm run typecheck`
- Lint/config changes -> `npm run lint`
- Core logic changes -> `npm test`
- UI flows/routing -> `npm run test:e2e`
- Auth/RLS issues -> `node scripts/check-rls-status.ts` or `node scripts/diagnose-rls.ts`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jelidia) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
