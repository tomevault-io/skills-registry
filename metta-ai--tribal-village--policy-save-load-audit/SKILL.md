---
name: policy-save-load-audit
description: Audit policy save/load and checkpoint handling across metta/cogames to find compatibility risks or legacy shims. Use when save/load behavior is in question. Use when this capability is needed.
metadata:
  author: metta-ai
---

# Policy Save/Load Audit

## Workflow
- Trace the save/load call graph (policy spec, checkpoint writer/reader).
- Check file formats and compatibility points (.mpt, safetensors, metadata).
- Flag risks or legacy shims and propose cleanup steps.
- Summarize findings with file paths.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/metta-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
