---
name: ui-ux-pro-max
description: Generate a UI/UX design system using local datasets and scripts. Use Use when this capability is needed.
metadata:
  author: darthlinuxer
---

# UI/UX Pro Max

Use this skill to generate a design system and UI/UX recommendations with the local dataset.

## Quick Start

```bash
python3 scripts/search.py "<product_type> <industry> <keywords>" --design-system -p "Project Name"
```

## Optional Commands

```bash

# Persist design system
python3 scripts/search.py "<query>" --design-system --persist -p "Project Name"

# Domain search
python3 scripts/search.py "<keyword>" --domain <domain> [-n <max_results>]

# Stack guidance
python3 scripts/search.py "<keyword>" --stack html-tailwind
```

## Notes
- Data lives in `data/`
- Scripts live in `scripts/`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/darthlinuxer) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
