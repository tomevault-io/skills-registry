---
name: ksp-module-router
description: Route tasks to processor, workload, or shared build/docs areas and enforce module boundaries before implementation. Use when this capability is needed.
metadata:
  author: doljae
---

# KSP Module Router

Use this skill at the beginning of feature/fix work in this repository.

## Routing Heuristics
- KSP traversal/filtering/code generation behavior -> `processor`
- Generated API usage examples or integration-style regressions -> `workload`
- Release docs/version matrix/dependency alignment -> root `README.md`, Gradle files, or `scripts`

## Guardrails
- Choose one primary module first.
- Avoid changing `workload` for processor-only concerns unless a behavior proof is required.
- Avoid changing processor logic when the task is documentation-only.
- If task scope is unclear, inspect existing tests before editing.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/doljae) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
