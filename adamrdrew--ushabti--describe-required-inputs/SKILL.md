---
name: describe-required-inputs
description: Mandatory files agents must read before acting. Load when starting agent work to ensure prerequisites are met. Use when this capability is needed.
metadata:
  author: adamrdrew
---

## Required Inputs

Before any agent acts, it must read:
- `.ushabti/laws.md` (if it exists)
- `.ushabti/style.md` (if it exists)

If laws or style don't exist when needed, stop and instruct the user to run the appropriate agent first.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adamrdrew) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
