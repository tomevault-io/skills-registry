---
name: yanhu-pr-review
description: Review changes like a strict backend TL: correctness, edge cases, performance, naming, tests, and operational safety. Use when this capability is needed.
metadata:
  author: fall4knight
---

Review checklist (must include):
- Data contracts: manifest/index/events schemas and backward compatibility
- Determinism & reproducibility: fixed seeds, stable sorting, stable timestamps
- Performance: ffmpeg calls, concurrency, caching, I/O layout
- Safety: no destructive ops; clear separation between code and generated artifacts
- Tests: at least one smoke test for CLI + one unit test for schema/segment logic

Output:
- Blockers (must fix)
- Non-blockers (should fix)
- Nice-to-haves
- Suggested next milestone

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fall4knight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
