---
name: fetch-page
description: Fetch a web page and return its text content Use when this capability is needed.
metadata:
  author: cesr
---

# Fetch Page

Fetches a URL and returns the page body as plain text (HTML tags stripped).

## Usage

Call `run_skill_script` with:
- **skill**: `fetch-page`
- **script**: `./scripts/fetch-page.ts`
- **input**: `{ "url": "https://example.com" }`

The script returns `{ url, status, content }` where `content` is the
text-only body (capped at ~32 000 chars to stay context-friendly).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cesr) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
