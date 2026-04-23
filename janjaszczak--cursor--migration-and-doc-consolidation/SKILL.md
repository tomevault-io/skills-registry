---
name: migration-and-doc-consolidation
description: Execute end-to-end migrations and consolidate docs/scripts (remove dead files, keep canonical runbooks). Use when changing runtime model, reorganizing repo, or doing “repo cleanup”. Use when this capability is needed.
metadata:
  author: janjaszczak
---

# migration-and-doc-consolidation

## Activation gate
Use if:
- Migration (tech/runtime), repo restructuring, “porządkowanie”, doc consolidation, script maintenance.

## Procedure
1. Inventory:
   - entrypoints, scripts, docs, CI workflows.
2. Identify canonical run path:
   - one “golden” command set.
3. Remove/flag dead scripts (with grep references).
4. Update docs to point to canonical paths.
5. Validate: build/test/lint where available.

## Output
- Summary of changes + new canonical runbook + verification commands.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/janjaszczak) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
