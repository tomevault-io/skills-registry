---
name: detect-commands
description: Infer and verify install/test/lint/build commands for the repo. Use when this capability is needed.
metadata:
  author: cheekycodexconjurer
---

## Purpose
Identify runnable commands and record them with confidence.

## Steps
1. Search for Makefile, package manifests, CI configs.
2. Propose install/test/lint/format/build commands.
3. Record in `COMMANDS.md` and `COMMANDS.json`.
4. Verify when safe, and update confidence.

## Guardrails
- Mark unverified commands with low confidence.
- Do not execute destructive commands.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cheekycodexconjurer) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
