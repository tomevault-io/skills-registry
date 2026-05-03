---
name: k-reader-panel
description: > Use when this capability is needed.
metadata:
  author: lluistfc
---

# Reader Panel UI (Right Side)

Knowledge for BookArchivist's right panel: book content display, navigation, actions.

## Quick Reference

| Feature | Module | Key Function |
|---------|--------|--------------|
| **Display** | `UI_Reader.lua` | `ShowBook(bookId)` (renders content) |
| **Rendering** | `UI_Reader_Rich.lua` | SimpleHTML + fallback (plain text) |
| **Navigation** | `UI_Reader_Navigation.lua` | Multi-page browsing (Prev/Next) |
| **Actions** | `UI_Reader_Actions.lua` | Favorite/Share/Delete buttons |

**Critical:** SimpleHTML requires `<html><body>` tags, falls back to plain text if missing.

## Full Documentation

See: [../../.github/copilot-skills/8-reader-panel.md](../../.github/copilot-skills/8-reader-panel.md)

Contains:
- ShowBook flow (ViewModel → render → track Recent)
- SimpleHTML rendering (HTML tags, color codes, formatting)
- Page navigation (multi-page books, Prev/Next buttons)
- Favorite button (star icon, Toggle)
- Share button (export to chat)
- Delete button (confirmation dialog, remove from DB)
- HTML detection (plain text fallback)
- Content sanitization (escape sequences)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lluistfc) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
