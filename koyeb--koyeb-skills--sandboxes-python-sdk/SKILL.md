---
name: sandboxes-python-sdk
description: Use the Koyeb Python SDK (koyeb-sdk) to create and manage sandboxes programmatically. Use when the user wants Python sandbox automation. Use when this capability is needed.
metadata:
  author: koyeb
---

# Koyeb Sandboxes (Python SDK)

## When to use

Use this skill when the user asks to manage Koyeb sandboxes from Python using the official SDK.

## Prerequisites

- Python environment
- Package installed: `koyeb-sdk`
- API token available as `KOYEB_API_TOKEN`

## Workflow

1. Install the SDK package.
2. Configure authentication using `KOYEB_API_TOKEN`.
3. Create, start, or stop sandboxes as needed.
4. Execute commands or retrieve logs/status through the SDK.
5. Clean up sandboxes when finished.

## Examples

- Install the SDK: `pip install koyeb-sdk`
- Use token auth: set `KOYEB_API_TOKEN` in your environment

## References

For detailed flags and SDK usage, see references/koyeb-sandbox-python-sdk.md.

- references/koyeb-sdk-auth.md
- references/koyeb-sandbox-python-sdk.md

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/koyeb) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
