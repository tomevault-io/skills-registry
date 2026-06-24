---
name: codeindex
description: Generate and search symbol indexes to navigate codebases with low token usage Use when this capability is needed.
metadata:
  author: nathandcornell
---

# Code Index Skill

Use CTags to generate a searchable symbol index for quick navigation.

## When to use

- Exploring an unfamiliar codebase
- Locating definitions without opening full files
- Mapping file structure before deep reads

## Scripts

Scripts live in `scripts/codeindex/`:

- `generate-index.sh [project-root]`
- `lookup.sh <symbol-pattern> [project-root]`
- `file-symbols.sh <file-path> [project-root]`

## Workflow

1. Generate the index for a project:
   ```bash
   ./scripts/codeindex/generate-index.sh /path/to/project
   ```
2. Search symbols before reading files:
   ```bash
   ./scripts/codeindex/lookup.sh handleAuth /path/to/project
   ```
3. Inspect a file's symbols:
   ```bash
   ./scripts/codeindex/file-symbols.sh src/api/auth.ts /path/to/project
   ```
4. Regenerate after edits to keep the index fresh.

## Index location

Indexes are written to `.codex/codeindex/` inside the target project:

- `.codex/codeindex/tags`
- `.codex/codeindex/files.txt`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nathandcornell) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
