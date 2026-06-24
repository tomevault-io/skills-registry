---
name: login
description: Authenticate with Vocal Bridge using your API key. Use when the user wants to connect Claude Code to their Vocal Bridge agent. Use when this capability is needed.
metadata:
  author: vocalbridgeai
---

Authenticate with Vocal Bridge to manage your voice agent.

## Prerequisites

First check if the CLI is installed:

```bash
which vb || command -v vb
```

If not found, install it:

```bash
pip install --upgrade vocal-bridge
```

## Login

Run: `vb auth login $ARGUMENTS`

If no API key is provided in $ARGUMENTS, tell the user they need to provide their API key. Example:

```
/vocal-bridge:login vb_your_api_key_here
```

The API key can be found on the agent detail page at https://vocalbridgeai.com

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vocalbridgeai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
