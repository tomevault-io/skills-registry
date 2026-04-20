---
name: semantic-rename
description: Run deterministic TypeScript symbol rename using compiler APIs instead of text replacement. Use when this capability is needed.
metadata:
  author: tbk0ng
---

Use this skill when a refactor needs a safe symbol rename across files.

Execution steps:
1. Identify the exact symbol location (file, 1-based line, 1-based column).
2. Run dry-run first:
   - `npm run refactor:rename -- --file <path> --line <line> --column <column> --newName <name> --dryRun`
3. Review output (`touchedFiles`, `touchedLocations`) and confirm scope.
4. Apply rename:
   - `npm run refactor:rename -- --file <path> --line <line> --column <column> --newName <name>`
5. Run verification:
   - `npm run verify:fast`
6. If verification fails repeatedly, run:
   - `npm run verify:loop -- -Profile fast -MaxAttempts 2`

Rules:
- Do not use plain find/replace for symbol rename tasks.
- Keep rename scoped to one change branch.
- Record decision and verify evidence in OpenSpec tasks and session journal.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tbk0ng) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
