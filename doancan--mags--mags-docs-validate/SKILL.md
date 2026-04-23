---
name: mags-docs-validate
description: Validate project documentation Use when this capability is needed.
metadata:
  author: doancan
---

# MAGS Docs Validate

Run validation checks on all project documents.

## Usage

```
/mags-docs-validate
```

## Steps

1. Ask the user: "Standard validation or deep validation?"
   - **Standard** — checks frontmatter, required sections, cross-references, freshness
   - **Deep** — all standard checks plus: version conflict detection, memory-doc consistency, ADR structure validation, and module completeness checks

   If deep: call `mags_validate_docs` with `deep: true`.
   If standard (or no preference): call `mags_validate_docs` with default parameters.
2. Display results grouped by severity:
   ```
   == Document Validation ==

   Errors (must fix):
     - docs/architecture/overview.md: Missing required section "Tech Stack"
     - docs/rules/backend.md: Broken internal link to ../api/auth.md

   Warnings (should fix):
     - docs/modules/auth.md: No code examples found
     - docs/changelog/changes.md: Last updated over 30 days ago

   Passed: <N>/<total> documents are healthy
   ```
3. If there are errors, ask: "Would you like me to fix the errors?"
4. If yes, read each problematic doc with `mags_get_doc`, fix the issues, and call `mags_update_doc`.

---

**Related commands:**
| Command | Description |
|---------|-------------|
| `/mags-docs` | List all project documents |
| `/mags-docs-search <query>` | Search across all documents |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/doancan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
