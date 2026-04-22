---
name: refactor-import-hygiene
description: | Use when this capability is needed.
metadata:
  author: jordangunn
---

# Instructions

Read all references in `references/` before using this skill.

## Signals

- Module-stutter was removed and symbols became intentionally generic (Metadata, Spec, File, Header, Payload)
- A refactor moved code into new namespaces and callsites were updated
- The diff introduces many `from X import Y` imports
- There are repeated name collisions or ambiguous identifiers in local scopes

## References

**Directory:** `references/`

- `01_GOAL.md`
- `02_DEFINITION.md`
- `03_RULES.md`
- `04_PROCEDURE.md`
- `05_OUTPUT.md`
- `06_EXAMPLES.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jordangunn) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
