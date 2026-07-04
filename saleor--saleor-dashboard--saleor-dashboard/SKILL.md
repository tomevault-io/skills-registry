---
name: saleor-dashboard-changesets
description: When asked to created changeset/changelog entry. Use when this capability is needed.
metadata:
  author: saleor
---

ALWAYS create changeset with severity `patch`, unless specified otherwise

Example changeset

```markdown
---
"saleor-dashboard": patch
---

<description>
```

description should

1. Focus on user-facing changes.
2. Do not expose internal/technical details, like tests or refactors
3. Provide pattern before/after if applicable

---
> Source: [saleor/saleor-dashboard](https://github.com/saleor/saleor-dashboard) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-07-04 -->
