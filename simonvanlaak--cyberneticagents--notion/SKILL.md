---
name: notion
description: Request timeout in seconds. Use when this capability is needed.
metadata:
  author: simonvanlaak
---

Use this skill to access the Notion API for read/write operations.

Notes:
1. `NOTION_API_KEY` is optional; without it the Notion API requests will fail.
2. Default Notion API version is `2025-09-03` unless overridden.
3. The CLI outputs JSON with `status_code`, `ok`, and `response`.

Examples:
- Search pages:
  - `method=POST`
  - `path=/v1/search`
  - `body={"query":"roadmap","page_size":5}`
- Retrieve a page:
  - `method=GET`
  - `path=/v1/pages/<page_id>`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/simonvanlaak) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
