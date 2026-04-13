---
name: llm-test
description: Guidelines for writing tests for LLM-related functionality Use when this capability is needed.
metadata:
  author: elie222
---
Read and follow `.claude/skills/testing/llm.md`.

For normal LLM tests, do not pin fixture users to a specific `aiProvider` / `aiModel`; leave them `null` unless the test is explicitly about model-selection behavior.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/elie222) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
