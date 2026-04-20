---
name: timestamp-epoch
description: Return an epoch window payload using the skill script and config. Use when this capability is needed.
metadata:
  author: therealtimex
---

# Epoch Window Payload

## When to Use
- The user asks for an epoch window or expiration payload.
- The user mentions "expires_at", "window", or "epoch window".

## Workflow
- [ ] Read `references/config.json` to confirm window seconds
- [ ] Run the skill script from `references/` with the shell tool
- [ ] Return the script output exactly as JSON

## Execution
- Read the config:
```
read_file "/absolute/path/to/skills/timestamp-epoch/references/config.json"
```
- Run the script:
```
python "/absolute/path/to/skills/timestamp-epoch/references/epoch_payload.py"
```

## Response Format
- Return the JSON output exactly as printed by the script
- Do not add extra keys

## Notes
- Do not compute the timestamp manually.
- Do not change window seconds from config.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/therealtimex) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
