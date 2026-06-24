---
name: copy
description: Copy content to my clipboard using `pbcopy`. Use when this capability is needed.
metadata:
  author: ivy
---

# Copy to Clipboard

## Arguments

```
$ARGUMENTS
```

## Instructions

### No Arguments / "last response"
Copy Claude's last response to the clipboard.

### File Path
If arguments look like a file path (starts with `/`, `~`, `./`, or contains common extensions):
1. Read the file with Read tool
2. Pipe contents to `pbcopy`

### Literal Content
Copy the provided text directly to clipboard.

## Implementation

```bash
pbcopy << 'EOF'
[content to copy]
EOF
```

## Examples

```
/copy                     → copy last response
/copy last response       → copy last response
/copy ~/notes.txt         → copy file contents
/copy Hello, world!       → copy literal text
/copy the function above  → copy referenced code block
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ivy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
