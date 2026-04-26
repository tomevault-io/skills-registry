---
name: outfitter-agents-check
description: Checks and configures Outfitter marketplaces and plugins. Use when setting up projects or checking plugin configuration. Use when this capability is needed.
metadata:
  author: outfitter-dev
---

# Agent Setup

Check and configure outfitter marketplaces in a project.

## Check Current Status

Checks both project (`.claude/settings.json`) and user (`~/.claude/settings.json`) levels.

!`bun ${CLAUDE_PLUGIN_ROOT}/skills/outfitter-agents-check/scripts/check-outfitter.ts .`

## Settings Structure

Claude Code settings use two keys for marketplace plugins:

- **`extraKnownMarketplaces`** — registers a marketplace by alias, pointing to a GitHub repo
- **`enabledPlugins`** — enables individual plugins using `<plugin>@<marketplace>` identifiers

The check script above reports the concrete identifiers and their current status. Use its output to determine what needs to be added.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/outfitter-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
