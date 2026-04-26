---
name: tonemana-apply
description: Choose a Tone & Manner pattern, produce an approved Tonemana Pack, and apply references into the UIUX Pack. Use when this capability is needed.
metadata:
  author: shunta-sato
---

# tonemana-apply

## Goal
Freeze one Tone & Manner decision as a versioned pack and apply it to the UIUX pack via references.
Treat tone as atmosphere and manner as explicit rules. Keep text constraints under writing_style/copy_style/text_rules.

## Steps
1) Ensure catalog exists (`tonemana/catalog/`). If missing, run tonemana-catalog behavior (create from templates).
2) Create `tonemana/YYYYMMDD-<slug>/` pack.
3) Update UIUX pack files to reference the chosen pattern and pack.
4) Write a short diff summary.

## Wizard (max 5 questions)
1) Confirm selected pattern id (one of 7)
2) Confirm touchpoints in scope
3) Confirm fixed assets (brand colors/fonts)
4) Confirm writing style constraints (polite/neutral/casual)
5) Confirm accessibility baseline

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shunta-sato) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
