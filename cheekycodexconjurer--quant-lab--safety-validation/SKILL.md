---
name: safety-validation
description: Validate changes against .agentignore before commit. Use when this capability is needed.
metadata:
  author: cheekycodexconjurer
---

## Purpose
Ensure forbidden zones are never modified.

## Steps
1. Compare modified paths against `.agentignore`.
2. Stop if any forbidden path is touched.
3. Record validation in `ACTION_LOG.md`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cheekycodexconjurer) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
