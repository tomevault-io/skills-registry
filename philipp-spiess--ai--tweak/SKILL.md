---
name: tweak
description: Open the user's default editor at a specific line to manually tweak styles or code. Use when the user wants to make manual adjustments after you've made changes. Use when this capability is needed.
metadata:
  author: philipp-spiess
---

# Tweak

Opens the user's default editor at a specific file and line number, allowing them to manually tweak the code.

## Usage

After making changes to a file, if the user invokes `/tweak`, run the `open-editor.sh` script from this skill directory:

```bash
~/.claude/skills/tweak/open-editor.sh <file_path> <start_line> [end_line]
```

## Instructions

1. When the user says `/tweak`, identify the **last file and line range you edited** in the conversation
2. Run the script with the file path, start line, and end line (for multi-line edits)
3. The script auto-detects which GUI editor is running (Cursor, VS Code, Zed, etc.) and opens the file with the range selected

## Example

Single line edit at line 42:
```bash
~/.claude/skills/tweak/open-editor.sh /Users/philipp/dev/app/src/Button.tsx 42
```

Multi-line edit from line 42 to 58 (opens at start line; range selection only works in Zed):
```bash
~/.claude/skills/tweak/open-editor.sh /Users/philipp/dev/app/src/Button.tsx 42 58
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/philipp-spiess) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
