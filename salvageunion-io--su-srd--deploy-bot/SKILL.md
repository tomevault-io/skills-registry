---
name: deploy-bot
description: Deploy Discord bot slash commands (test guild or global) Use when this capability is needed.
metadata:
  author: salvageunion-io
---

# Deploy Bot Commands

Deploy Discord bot slash commands.

If `$ARGUMENTS` contains "global":
1. Run `bun run deploy-commands:global` to deploy globally (production)
2. Warn that global commands can take up to 1 hour to propagate

Otherwise:
1. Run `bun run deploy-commands` to deploy to test guild only
2. Test guild commands update instantly

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/salvageunion-io) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
