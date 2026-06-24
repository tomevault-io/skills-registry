---
name: refine-a2ui
description: > Use when this capability is needed.
metadata:
  author: gqadonis
---

# Refine A2UI

Invoke the PMPO artifact refinement loop for **A2UI specification** artifacts.

## Setup

1. Set `artifact_type: a2ui`
2. Load domain adapter from `references/domain/a2ui.md`
3. Load template from `assets/templates/a2ui-preview-template.html`
4. Start the PMPO loop via `prompts/meta-controller.md`

## User Input

The user will provide: $ARGUMENTS

Parse the arguments for:
- A2UI component or specification name
- Schema requirements (JSON Schema, TypeScript types)
- Structural integrity needs
- Normalization rules
- Preview rendering requirements

## Default Constraints

- Validate A2UI spec structural integrity
- Ensure schema compliance
- Normalize field naming conventions
- Generate preview HTML using template
- Update manifest with specification files

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gqadonis) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
