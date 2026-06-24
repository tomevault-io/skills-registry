---
name: code-review
description: When coding is done and changes need review → multi-dimensional code review (SOLID, security, quality, edge cases), outputs structured report. Use when this capability is needed.
metadata:
  author: cklxx
---

# code-review

Run a multi-dimensional code review (SOLID architecture, security, quality, edge cases, cleanup) on the current diff and output a structured report with severity levels (P0-P3). All review checklists, workflow steps, and report generation are handled by run.py.

## 调用

```bash
python3 skills/code-review/run.py review
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cklxx) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
