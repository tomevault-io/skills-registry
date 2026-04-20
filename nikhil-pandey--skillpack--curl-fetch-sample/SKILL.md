---
name: curl-fetch-sample
description: Efficiently fetch and inspect remote content with curl. Use when asked to read or inspect a URL/API response without dumping full content; download to /tmp and use head/jq/rg/sed/wc/file to sample. Use when this capability is needed.
metadata:
  author: nikhil-pandey
---

Fetch remote content to /tmp, inspect with small samples, avoid dumping whole files into context.

Workflow

1) Pick temp path
- Use mktemp; keep extension if obvious
- Note path in output

2) Download
- curl -fsSL --compressed -o "$tmp" "$url"
- Add headers/flags only if required; avoid echoing secrets

3) Identify and size
- file -b "$tmp"
- wc -l "$tmp"
- head -n 40 "$tmp"

4) Inspect by type
- JSON: jq '.' "$tmp" | head -n 120
- JSON array: jq -c '.[]' "$tmp" | head -n 50
- NDJSON: head -n 50 "$tmp" | jq -c '.'
- Text/log: rg -n "pattern" "$tmp"; sed -n '1,160p' "$tmp"
- CSV/TSV: head -n 50 "$tmp"

5) Share findings
- Report path, size/line count, short excerpts only
- Ask before deeper slices; never paste full file

6) Cleanup
- Leave /tmp unless user asks to remove

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nikhil-pandey) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
