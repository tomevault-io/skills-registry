---
name: pend
description: View latest reply from AI provider (gemini/codex/opencode/droid/claude). Use when this capability is needed.
metadata:
  author: bfly123
---

# Pend - View Latest Reply

View the latest reply from specified AI provider.

## Usage

The first argument must be the provider name:
- `gemini` - View Gemini reply
- `codex` - View Codex reply
- `opencode` - View OpenCode reply
- `droid` - View Droid reply
- `claude` - View Claude reply

Optional: Add a number N to show the latest N conversations.

## Execution (MANDATORY)

```bash
pend $ARGUMENTS
```

## Examples

- `/pend gemini`
- `/pend codex 3`
- `/pend claude`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bfly123) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
