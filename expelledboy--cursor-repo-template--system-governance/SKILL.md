---
name: system-governance
description: Use when you need to understand authority, resolve conflicts, trace governed_by chains, or validate what governs a document.
metadata:
  author: expelledboy
---

# Purpose
Navigate and apply the documentation governance system to ensure edits respect authority chains and SSOT principles.

# Scope
Covers the `governed_by` DAG, authority model, conflict resolution, and validation procedures. Does not cover runtime-specific mechanics (see `system-context-mechanics`) or task state (see `system-objective-state`).

# Minimal Path
1) Before editing any doc, check its `governed_by` frontmatter to identify constraints.
2) Run `just docs-index` to visualize the governance DAG if relationships are unclear.
3) If a conflict exists between docs, apply `docs/system/procedure/resolving-conflicts.md`.
4) If creating a new doc, ensure it declares `governed_by` per the appropriate model doc.
5) Run `just docs-validate` to verify all linkages are correct.

# Validation
- `just docs-validate` passes with no errors.
- Every doc has a valid `governed_by` chain to a root authority.
- No circular references exist in the DAG.

# Failure Modes
- Editing a doc without checking what governs it (authority violation).
- Creating orphan docs with no `governed_by` declaration.
- Duplicating content instead of linking (SSOT violation).

# References
- `docs/system/governance.md`: Master governance definition
- `docs/system/authority-model.md`: Authority layer definitions
- `docs/system/procedure/resolving-conflicts.md`: Conflict resolution procedure
- `docs/system/procedure/validating-doc-contracts.md`: Validation procedure

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/expelledboy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
