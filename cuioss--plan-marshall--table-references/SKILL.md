---
name: table-references-test
description: Test skill with table-format references Use when this capability is needed.
metadata:
  author: cuioss
---

# Table References Test Skill

This skill tests that scripts referenced in markdown tables are properly detected.

## External Resources

### Scripts (scripts/)

| Script | Mode | Purpose |
|--------|------|---------|
| `analyze-data.sh` | **EXECUTE** | Analyzes data files |
| `validate-input.py` | **EXECUTE** | Validates input format |

### References (references/)

| Reference | Purpose |
|-----------|---------|
| `guide.md` | Main guide documentation |

## Workflow

1. Execute `scripts/analyze-data.sh`
2. Run validation with `scripts/validate-input.py`
3. See `references/guide.md` for details

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cuioss) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
