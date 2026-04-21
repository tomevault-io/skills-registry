---
name: watcher-runner-filesystem
description: > Use when this capability is needed.
metadata:
  author: sofia-asif
---

# Watcher Runner (Filesystem)

Start and validate the Bronze-tier watcher that turns local file drops into `Needs_Action/*.md` items.

## Assumptions

- Project code lives under `MY-AI-EMPLOYEE/` (user-provided convention).
- The watcher script exists in that project (commonly `watchers/filesystem_watcher.py` or similar).
- The watcher needs two paths:
  - Vault root path
  - Watched drop folder path

If any of these are unknown, stop and ask for the paths.

## Run workflow

1. Locate the watcher entrypoint.
2. Verify Python environment setup (uv/venv) and dependencies.
3. Run watcher in foreground first (for visible logs).
4. Trigger a file creation in the watched folder.
5. Confirm a new action item appears in `Needs_Action/`.

## Debug workflow

1. Confirm the watcher process is running.
2. Confirm filesystem events are firing (some environments require polling).
3. Confirm vault path is correct and writable.
4. Confirm watcher is writing `.md` action files (not only copying binaries).

## Resources

- Reference: `references/runbook.md`
- Example smoke test script: `scripts/create_test_drop.py`
- Examples: `examples.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sofia-asif) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
