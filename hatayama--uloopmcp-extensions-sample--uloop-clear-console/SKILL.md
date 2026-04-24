---
name: uloop-clear-console
description: Clear Unity console logs via uloop CLI. Use when you need to: (1) Clear the console before running tests, (2) Start a fresh debugging session, (3) Clean up log output for better readability. Use when this capability is needed.
metadata:
  author: hatayama
---

# uloop clear-console

Clear Unity console logs.

## Usage

```bash
uloop clear-console [--add-confirmation-message]
```

## Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `--add-confirmation-message` | boolean | `false` | Add confirmation message after clearing |

## Examples

```bash
# Clear console
uloop clear-console

# Clear with confirmation
uloop clear-console --add-confirmation-message
```

## Output

Returns JSON confirming the console was cleared.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hatayama) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
