---
name: claude-skill-registry
description: name: resilience-check Use when this capability is needed.
metadata:
  author: majiayu000
---
---
name: resilience-check
description: Checklist for reliability engineering
---

## Procedure

1. Check for `try/catch` blocks around async operations.
2. Verify timeouts are set on external calls.
3. Ensure fallbacks exist for failed data fetches.
4. Validate input types at boundaries.
5. Check for proper error logging.

---
> Source: [majiayu000/claude-skill-registry](https://github.com/majiayu000/claude-skill-registry) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-07-06 -->
