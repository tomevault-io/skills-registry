---
name: history-and-localstorage
description: Use when changing debate history, localStorage, or timing logic (e.g., `app/page.tsx`).
metadata:
  author: sanmak
---
# Goal
Keep history persistence stable, de-duplicated, and backward compatible.

# Do
- Preserve `HISTORY_KEY`/`HISTORY_LIMIT` behavior.
- Avoid re-saving when loading a history entry.
- Keep legacy storage readable with safe fallbacks.
- If the schema changes, add migration or versioned fields.

# Don't
- Break existing localStorage entries or inflate history with duplicates.

# Examples
- "Change debate history card layout without touching persistence."
- "Add a new field to stored history entries with a fallback."

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sanmak) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
