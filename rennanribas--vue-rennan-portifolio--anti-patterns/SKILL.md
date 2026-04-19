---
name: anti-patterns
description: References for what to avoid in AI-assisted coding. Use when planning, reviewing, or verifying agent output. Use when this capability is needed.
metadata:
  author: rennanribas
---

# Anti-patterns (references)

- **Assumption bias:** Prefer asking for missing requirements over inferring. Do not force a solution when constraints are unclear.
- **Doom loop:** Avoid repeated edits that rewrite the same area. One coherent pass; then verify (lint, type-check) and fix only real failures.
- **Vibe coding:** Do not ship unverified output. Every change must pass project checks (e.g. `npm run lint`, `npm run type-check`) before “done.”
- **Scope creep:** Implement only what the plan or request specifies. No “while we’re here” additions without explicit approval.
- **Copy-paste bulk:** Prefer pointing to canonical examples (AGENTS.md, existing components) over inlining long blocks. Keep context small and precise.

When in doubt: verify first, then edit; ask when ambiguous.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rennanribas) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
