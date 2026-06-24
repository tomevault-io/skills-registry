---
name: status
description: Check Vocal Bridge authentication status. Shows current API key, connected agent name, and connection health. Use when this capability is needed.
metadata:
  author: vocalbridgeai
---

Check the current Vocal Bridge authentication status.

First ensure CLI is installed:

```bash
pip install --upgrade vocal-bridge
```

Then run:

```bash
vb auth status
```

This shows:
- API URL
- API Key (masked)
- Authentication status
- Connected agent name and mode
- Agent ID
- Phone number (if assigned)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vocalbridgeai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
