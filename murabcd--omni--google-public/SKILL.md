---
name: google-public
description: Public Google Docs/Sheets tools (no OAuth). Use when this capability is needed.
metadata:
  author: murabcd
---

# google-public

Tools for reading publicly shared Google Docs/Sheets/Slides by link.

Quick start
- Telegram: `/skill <name> <json>`

Runtime skills
- Each tool can be wrapped as a runtime skill in `apps/bot/skills/<name>/skill.json`.
- The `tool` field supports `google-public.<tool_name>`.

Available tools

Docs
- `google_public_doc_read`

Sheets
- `google_public_sheet_read`

Slides
- `google_public_slides_read`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/murabcd) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
