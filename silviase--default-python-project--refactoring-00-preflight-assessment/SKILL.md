---
name: refactoring-00-preflight-assessment
description: Use when doing a quick preflight to assess scope, risks, and priorities for Python research code refactoring.
metadata:
  author: silviase
---

# Refactoring 00: Preflight Assessment

## Goal

Spend a few minutes to scope the refactor, identify risks, and choose an order of work.

## Sequence

- Order: 00
- Previous: none
- Next: refactoring-01-project-structure

## Workflow

- Scan the repo layout, entrypoints, and main data paths.
  - Success: Entry points and key data paths are listed.
- List critical risks: data availability, runtime constraints, hardware/GPU, external tools.
  - Success: Risks are enumerated with brief impact notes.
- Identify fragile areas (global state, hidden config, ad hoc scripts).
  - Success: Fragile hotspots are called out with file or module names.
- Decide a minimal, sequential plan and confirm with the user.
  - Success: A short ordered plan is agreed with the user.
- Write a short checklist of what will and will not be touched.
  - Success: In-scope and out-of-scope items are explicitly captured.

## Guardrails

- Do not change code in this step.
- Keep the assessment under a few minutes unless requested.
- Prefer explicit tradeoffs over broad promises.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/silviase) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
