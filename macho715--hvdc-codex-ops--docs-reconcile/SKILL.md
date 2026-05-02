---
name: docs-reconcile
description: description: Reconcile normalized CI/PL/BL fields and output mismatch report Use when this capability is needed.
metadata:
  author: macho715
---
﻿---
name: docs-reconcile
description: Reconcile normalized CI/PL/BL fields and output mismatch report
---
Instructions:
- Inputs:
  - data/ci_fields.csv (template: data/templates/ci_fields.template.csv)
  - data/pl_fields.csv (template: data/templates/pl_fields.template.csv)
  - data/bl_fields.csv (template: data/templates/bl_fields.template.csv)
- Run: powershell -ExecutionPolicy Bypass -File .codex/skills/docs-reconcile/scripts/run.ps1
- Output: reports/docs_reconcile.md
- Fail-safe: if inputs/columns missing, output only the '以묐떒' table.
Evidence Required:
- Report must include mismatch count + field-by-field status table.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/macho715) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
