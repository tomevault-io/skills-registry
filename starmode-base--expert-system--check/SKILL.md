---
name: check
description: Run TypeScript type check, ESLint, and Prettier formatting. Always run this check before returning code to the user. Use when this capability is needed.
metadata:
  author: starmode-base
---

Run all code quality checks and formatting:

```bash
npx tsc --noEmit && bun run lint && bun run format
```

This runs TypeScript type checking, ESLint with auto-fix, and Prettier formatting across the project.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/starmode-base) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
