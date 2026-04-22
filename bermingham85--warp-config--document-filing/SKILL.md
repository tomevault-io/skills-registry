---
name: document-filing
description: Every artifact must be filed in correct location with version control. Defines filing structure and naming conventions. Use when this capability is needed.
metadata:
  author: bermingham85
---

# Document Control and Filing

## Rule
Every artifact must be filed in the correct location with proper version control.

## Filing Structure
- _artifacts/{project-name}/ - Project-specific outputs
- _scripts/ - Reusable scripts
- BLUEPRINTS/ - System designs and plans
- MASTER_PROMPT_LIBRARY/ - Prompts for reuse
- SYSTEM_DOCS/ - Documentation

## Naming Conventions
- Scripts: action_target.py
- Prompts: PURPOSE_PROMPT.md
- Blueprints: SYSTEM_BLUEPRINT.md

## Anti-Patterns (FORBIDDEN)
- Files on Desktop
- Duplicate files in multiple locations
- Unnamed files like test.py or temp.txt

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bermingham85) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
