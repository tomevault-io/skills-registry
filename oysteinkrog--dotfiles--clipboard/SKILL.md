---
name: clipboard
description: | Use when this capability is needed.
metadata:
  author: oysteinkrog
---

# Copy to Clipboard (WSL/Windows)

Copy the provided text to the Windows clipboard with correct Unicode encoding.

## Instructions

1. Write the text to a temp file as UTF-8
2. Convert to UTF-16LE with BOM and pipe to `clip.exe`
3. Clean up the temp file

Use this pattern:

```bash
tmpfile=$(mktemp) && cat <<'CLIP_EOF' > "$tmpfile"
<TEXT TO COPY>
CLIP_EOF
iconv -f utf-8 -t utf-16le "$tmpfile" | clip.exe && rm -f "$tmpfile"
```

Important:
- Always use `iconv -f utf-8 -t utf-16le` before piping to `clip.exe` — without this, Unicode characters (em dashes, curly quotes, etc.) get garbled
- Use a heredoc with `'CLIP_EOF'` (quoted) to prevent shell expansion
- Confirm to the user when done

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/oysteinkrog) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
