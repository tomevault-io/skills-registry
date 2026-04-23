---
name: pp-solution-validator
description: > Use when this capability is needed.
metadata:
  author: kevcofett
---

# Power Platform Solution Validator

> **⚠️ DEPRECATED:** This skill is now part of **CMAP Validation Suite**.
> Use the main `cmap-validation` skill for full functionality.

## Redirect to CMAP Validation

The Power Platform Solution Validator has been merged into the CMAP Validation Suite,
which now includes:

- **Solution Governor** - Unified validation AND auto-fix
- **Zero Tolerance Policy** - 0 errors AND 0 warnings required
- **500+ validation rules** - Comprehensive Power Platform coverage
- **20 auto-fix rules** - SS001-SS020 for common import failures

## Quick Start

```bash
# Use Solution Governor (validates AND fixes)
python scripts/solution_governor.py solution.zip --fix

# Expected output: 0 errors, 0 warnings
```

## See Also

- [cmap-validation/SKILL.md](../cmap-validation/SKILL.md) - Full documentation
- [cmap-validation/scripts/](../cmap-validation/scripts/) - All validators

## Version History
- v1.0-v3.1 - Original Power Platform Solution Validator
- v7.09 - Merged into CMAP Validation Suite
- v7.10.0 - Deprecated in favor of cmap-validation with Solution Governor

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kevcofett) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
