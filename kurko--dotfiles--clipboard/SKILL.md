---
name: clipboard
description: Copy text to the system clipboard. Use when user asks to copy something, send to clipboard, or similar requests. Use when this capability is needed.
metadata:
  author: kurko
---

# Clipboard

Copy content to the user's system clipboard.

## How to Copy

Use a heredoc to reliably handle multi-line content, quotes, and special characters:

```bash
cat << 'EOF' | pbcopy
Your content here.
Can span multiple lines.
Handles "quotes" and 'apostrophes' without escaping.
EOF
```

## Platform Commands

- **macOS**: `pbcopy` (copy) / `pbpaste` (verify)
- **Linux**: `xclip -selection clipboard` or `xsel --clipboard`

## After Copying

Verify the clipboard content worked:

```bash
pbpaste | head -3
```

If verification shows different content, the sandbox may be blocking clipboard access. In that case, inform the user and display the text directly so they can copy it manually.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kurko) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
