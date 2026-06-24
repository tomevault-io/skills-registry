---
name: clipboard
description: Read or write the macOS clipboard. Use when copying/pasting text, transferring data between apps, or accessing clipboard contents. Use when this capability is needed.
metadata:
  author: mikeyobrien
---

# Clipboard Operations

## Read clipboard
```bash
pbpaste
```

## Write to clipboard
```bash
echo "text to copy" | pbcopy
```

## Copy file contents
```bash
pbcopy < file.txt
```

## Copy command output
```bash
ls -la | pbcopy
```

Returns the clipboard contents as plain text. Binary/image data is not supported via these commands.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mikeyobrien) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
