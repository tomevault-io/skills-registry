---
name: boot-builder
description: Bootstrap phase context for Builder. Provides current phase, next step, and full phase file contents. Use when this capability is needed.
metadata:
  author: adamrdrew
---

# Phase Bootstrap

Run the below command to get the active phase context. This replaces reading phase.md, steps.md, and progress.yaml individually. Use this to orient, then proceed directly to implementation.

```
python3 ${CLAUDE_PLUGIN_ROOT}/skills/boot-builder/boot.py
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adamrdrew) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
