---
name: open-url
description: Open URLs, files, and applications on macOS. Use for launching browsers, opening documents, or starting apps. Use when this capability is needed.
metadata:
  author: mikeyobrien
---

# Open URLs and Files

## Open a URL in default browser
```bash
open https://example.com
```

## Open a file with default app
```bash
open document.pdf
open image.png
```

## Open with a specific app
```bash
open -a "Safari" https://example.com
open -a "Visual Studio Code" project/
open -a "Finder" /path/to/directory
```

## Reveal in Finder
```bash
open -R /path/to/file
```

## Open a new Finder window
```bash
open .
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mikeyobrien) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
