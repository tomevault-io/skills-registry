---
name: web-fetch
description: Fetch web page content as Markdown using trafilatura. Use when sub-agents or agents need to read web page content but don't have access to the web_fetch tool. Works via shell with a Python one-liner. Use when this capability is needed.
metadata:
  author: tkykenmt
---

# Web Fetch (trafilatura)

Fetch web page content as Markdown text. Replacement for `web_fetch` tool in contexts where it's unavailable (e.g., sub-agents).

## Usage

```bash
python -c "import trafilatura; print(trafilatura.extract(trafilatura.fetch_url('URL'), output_format='markdown') or '')"
```

## Notes

- Returns empty string if extraction fails
- Automatically removes navigation, footers, sidebars
- Requires `trafilatura` package (in `requirements.txt`)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tkykenmt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
