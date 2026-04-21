---
name: move-a-file
description: Safely move or rename a file while finding and updating every reference across the codebase. Use when this capability is needed.
metadata:
  author: ayazhankadessova
---

# Move a File

Relocates or renames a single file and systematically updates all references to it — imports, documentation links, configuration entries, and build files.

## Inputs

- **Source path**: Current location of the file (relative to project root)
- **Destination path**: Where the file should end up
- **Context** (optional): Why the file is being moved, or what it contains

## Procedure

### 1. Validate Paths

Before doing anything, confirm:
- The source file exists
- The destination directory exists (or can be created)
- Nothing already lives at the destination path
- Both paths are relative to the project root

If any check fails, report the problem and stop.

### 2. Find Every Reference

Search the entire codebase for anything that points to the file. Use multiple search patterns to catch all variations:

| Pattern type | Example |
|-------------|---------|
| Bare filename | `config.py` |
| Full relative path | `src/utils/config.py` |
| With leading `./` | `./src/utils/config.py` |
| Without extension | `config` (for import statements) |
| In import syntax | `from src.utils import config` |

**Scan these file types at minimum:**
- Source code (`.py`, `.js`, `.ts`, `.go`, `.rs`, `.c`, `.cpp`, `.sh`)
- Documentation (`.md`, `.txt`, `.rst`)
- Configuration (`.yaml`, `.yml`, `.json`, `.toml`)
- Build files (`Makefile`, `CMakeLists.txt`, `package.json`)

### 3. Update Each Reference

For every file that references the moved file:
1. Read the file to understand the context of the reference
2. Compute the correct new path (it may need to be relative to *that* file's location)
3. Apply the edit
4. Note what was changed

**Path guidelines**:
- Documentation and config files typically use paths relative to the project root
- Import statements follow the language's own conventions
- Filesystem operations may need paths relative to the calling file

### 4. Perform the Move

Use `git mv` to preserve history:
```bash
git mv "<source>" "<destination>"
```

If the destination directory doesn't exist yet:
```bash
mkdir -p "$(dirname '<destination>')" && git mv "<source>" "<destination>"
```

### 5. Report Results

Summarise what happened:
- Old path and new path
- Number of files updated
- List of each updated file with a brief note on the change
- Any references that look uncertain and may need manual review

## Important Considerations

1. **Always use `git mv`** — it preserves file history. Never use plain `mv`.
2. **Case sensitivity** — git is case-sensitive even on case-insensitive filesystems. Take care when only changing letter case.
3. **Search broadly** — different files may reference the same path in different ways. Use multiple patterns.
4. **Flag uncertainty** — if a reference is ambiguous, include it in the report and ask the user to verify.
5. **Single files only** — this skill handles one file at a time. Moving entire directories requires a different approach.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ayazhankadessova) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
