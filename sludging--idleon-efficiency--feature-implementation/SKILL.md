---
name: feature-implementation
description: Step-by-step guide for implementing new game features and domain logic Use when this capability is needed.
metadata:
  author: sludging
---

# Feature Implementation Guide

For comprehensive implementation documentation, read [docs/NEW_FEATURE_IMPLEMENTATION_GUIDE.md](docs/NEW_FEATURE_IMPLEMENTATION_GUIDE.md).

## Key Reminders

**Prerequisites to gather before starting:**
- Save file keys for the feature's data
- WikBot data availability (`../IdleonWikiBot/exported/ts/`)
- Similar existing features to model after
- Navigation placement in UI
- Asset names from game files
- Check `reference-repos/IdleonToolbox/` for reference implementations

**Critical constraints:**
- DO NOT modify auto-generated directories: `data/domain/data/`, `data/domain/enum/`, `data/domain/model/`
- Calculate phase has strict dependency ordering - place your feature AFTER dependencies, BEFORE dependents
- Testing is MANDATORY for domain logic - use `/testing` skill
- Always do visual validation against the actual game

**Common patterns to follow:**
- Study similar features: `stamps.tsx` (simple), `alchemy.tsx` (medium), `sailing.tsx` (complex)
- Use Grommet UI components for consistency

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sludging) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
