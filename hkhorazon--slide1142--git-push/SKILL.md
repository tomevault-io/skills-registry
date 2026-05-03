---
name: git-push
description: Workflow to auto-export slides, update index, and sync to git. Triggered ONLY when user explicitly types '/git-push'. Use when this capability is needed.
metadata:
  author: hkhorazon
---

# Git Push Workflow

This skill automates the process of updating the course materials and syncing them to the repository.

## Workflow Status

- **Trigger**: `/git-push` (Slash command only)
- **Action**:
  1.  **Generate Maps**: Calls `slide` skill's `generate_map.py` to update course maps (respects `MapLock`).
  2.  **Export Slides**: Calls `slide` skill's `export.py` to convert all `.md` files to PDF.
  3.  **Cleanup Old PDFs**: Calls `slide` skill's `cleanup_pdf.py` to remove orphaned files.
  4.  **Update Index**: Regenerates `display/index.html` to reflect cleaner PDF list, then syncs to root `index.html`.
  5.  **Git Sync**: Adds all files, commits with "Auto update", and pushes to remote.

## Usage

Run the following command in the project root:

```bash
python .agent/skills/git-push/scripts/push.py
```

## Dependencies

- Requires `.agent/skills/slide/scripts/export.py` to exist.
- Requires `git` to be configured.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hkhorazon) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
