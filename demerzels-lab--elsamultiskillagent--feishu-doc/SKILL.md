---
name: feishu-doc
description: Fetch content from Feishu (Lark) Wiki, Docs, Sheets, and Bitable. Automatically resolves Wiki URLs to real entities and converts content to Markdown. Use when this capability is needed.
metadata:
  author: demerzels-lab
---

# Feishu Doc Skill

Fetch content from Feishu (Lark) Wiki, Docs, Sheets, and Bitable.

## Usage

```bash
node index.js fetch <url>
```

## Configuration

Create a `config.json` file in the root of the skill or set environment variables:

```json
{
  "app_id": "YOUR_APP_ID",
  "app_secret": "YOUR_APP_SECRET"
}
```

Environment variables:
- `FEISHU_APP_ID`
- `FEISHU_APP_SECRET`

## Supported URL Types

- Wiki
- Docx
- Doc (Legacy)
- Sheets
- Bitable

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/demerzels-lab) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
