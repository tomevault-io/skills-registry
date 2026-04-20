---
name: start
description: Start the development server with live reload Use when this capability is needed.
metadata:
  author: jxxcarlson
---

Start the Hakyll development server:

```bash
sh scripts/refresh.sh
```

This will:
1. Kill any existing process on port 8000
2. Convert any PNG/JPG images to WebP format
3. Build the Haskell project
4. Regenerate the site
5. Start the watch server at http://localhost:8000 with live reload

Note: To also watch the archive directory for changes, run `/watch-archive` in a separate terminal.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jxxcarlson) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
