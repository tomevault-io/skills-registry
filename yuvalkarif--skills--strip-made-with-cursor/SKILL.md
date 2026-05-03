---
name: strip-made-with-cursor
description: Set up Cursor hooks to automatically strip "Made with Cursor" branding from code, commits, and PR text before they are written. Use when the user wants to remove Cursor branding, strip "Made with Cursor" text, or add a preToolUse hook to prevent Cursor attribution. Use when this capability is needed.
metadata:
  author: yuvalkarif
---

# Setup Strip Made With Cursor

Sets up a `preToolUse` hook that intercepts tool calls and silently strips "Made with Cursor" (and its markdown-link variant) from any string values before Cursor writes them.

## What Gets Stripped

- `Made with Cursor` (case-insensitive, plain text)
- `Made with [Cursor](...)` (case-insensitive, markdown link variant)

When jq is available, the text is silently removed and the tool call proceeds. When jq is missing, the tool call is denied with a message asking the user to install jq.

## Steps

### 1. Ask scope

Ask the user: install for **this project only** (`.cursor/hooks.json`) or **globally** (`~/.cursor/hooks.json`)?

### 2. Copy the hook script

The bundled script is at: [scripts/strip-made-with-cursor.sh](scripts/strip-made-with-cursor.sh)

Copy it to the target location based on scope:

- **Project**: `.cursor/hooks/strip-made-with-cursor.sh`
- **Global**: `~/.cursor/hooks/strip-made-with-cursor.sh`

Make it executable with `chmod +x`.

### 3. Add hook to hooks.json

Add to the appropriate `hooks.json` file:

**Project** (`.cursor/hooks.json`):

```json
{
  "version": 1,
  "hooks": {
    "preToolUse": [
      {
        "command": "bash .cursor/hooks/strip-made-with-cursor.sh"
      }
    ]
  }
}
```

**Global** (`~/.cursor/hooks.json`):

```json
{
  "version": 1,
  "hooks": {
    "preToolUse": [
      {
        "command": "bash ~/.cursor/hooks/strip-made-with-cursor.sh"
      }
    ]
  }
}
```

If the `hooks.json` file already exists, merge the hook entry into the existing `hooks.preToolUse` array — don't overwrite other hooks.

### 4. Verify

Run a quick test:

```bash
echo '{"tool_name":"edit_file","tool_input":{"content":"Made with [Cursor](https://cursor.com)"}}' | bash <path-to-script>
```

Should return JSON with `"decision":"allow"` and the branding text removed from `updated_input`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yuvalkarif) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
