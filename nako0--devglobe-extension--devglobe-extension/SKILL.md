---
name: status
description: Set a status message on your DevGlobe profile Use when this capability is needed.
metadata:
  author: Nako0
---

Update the DevGlobe status message. User arguments: $ARGUMENTS

## Error cases

- If `$ARGUMENTS` is empty: show usage `/devglobe:status Your message here` (max 100 characters)
- If the message is longer than 100 characters: show error with current length

## Steps

The bundled status script reads the API key from `~/.devglobe/config.toml` directly — no need to extract it here. Just pipe the message:

```bash
echo '{"message":"<MESSAGE>"}' | node "${CLAUDE_PLUGIN_ROOT}/dist/update-status.js"
```

Escape JSON special characters in the message before substitution.

If the script returns `{"error":"not configured — run /devglobe:setup first"}`, tell the user to run `/devglobe:setup YOUR_API_KEY` first (key available at https://devglobe.app).

## Output

- On success (`{"ok":true}`): confirm the status was set, echo the message
- On error: show the error

---
> Source: [Nako0/devglobe-extension](https://github.com/Nako0/devglobe-extension) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-19 -->
