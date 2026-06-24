---
name: cursor-remarkable-upload
description: Upload markdown (including docmgr ticket docs) to a reMarkable device as PDF via a local uploader script, with a safe dry-run and optional mirroring of ticket structure. Use when the user asks to send docs to reMarkable, export markdown to PDF for reMarkable, or troubleshoot rmapi/pandoc/xelatex. Use when this capability is needed.
metadata:
  author: go-go-golems
---

# Cursor Remarkable Upload

## Overview

Safely convert `.md` to `.pdf` and upload to a reMarkable device, defaulting to dry-runs and avoiding overwrites.

## Preconditions

1. Confirm uploader exists: `python3 /home/manuel/.local/bin/remarkable_upload.py --help`
2. Confirm dependencies if needed: `rmapi`, `pandoc`, `xelatex`

## Workflow (safe default)

1. Choose markdown file(s) (use absolute paths).
2. Dry-run:
   - `python3 /home/manuel/.local/bin/remarkable_upload.py --dry-run /abs/path/to/doc.md`
3. Upload (no overwrite):
   - `python3 /home/manuel/.local/bin/remarkable_upload.py /abs/path/to/doc.md`
4. Only if explicitly requested: overwrite with `--force`.

## Ticket-aware uploads
- Prefer mirroring ticket structure to avoid name collisions; see `references/remarkable.md`.

## Reference
- Full usage + troubleshooting: `references/remarkable.md`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/go-go-golems) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
