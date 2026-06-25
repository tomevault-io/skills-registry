---
name: docs-coverage-and-route-integrity-audit
description: Audit docs.json route integrity, legacy path drift, and potential orphan docs files in v2. Use when this capability is needed.
metadata:
  author: livepeer
---

SKILL: Docs Coverage and Route Integrity Audit

Goal
Surface navigation and route correctness gaps before they become broken-doc regressions.

Command
```bash
node tools/scripts/docs-coverage-and-route-integrity-audit.js --scope full
```

Outputs
- `tasks/reports/repo-ops/docs-coverage-and-route-integrity-audit.md`
- `tasks/reports/repo-ops/docs-coverage-and-route-integrity-audit.json`

Checks
- Missing routes from navigation reports
- Legacy `/v2/pages/` references
- Candidate non-nav docs files for triage

---
> Source: [livepeer/docs](https://github.com/livepeer/docs) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-25 -->
