---
name: google-fonts-skill
description: | Use when this capability is needed.
metadata:
  author: podo
---

# google-fonts-skill

> Catalogue stub — full platform skill: [sliday/google-fonts-skill](https://github.com/sliday/google-fonts-skill)

## Decision tree

1. **Is the skill already active?** Check if the `google-fonts` MCP server is connected or the plugin is installed.
   - Yes → proceed directly
   - No → go to step 2

2. **Which install method fits your environment?**
   - MCP server → install with `uvx` (see below)
   - Claude plugin → install with `claude plugin install` (see below)
   - If unsure → show both options to the user

## Install methods

### Option A — MCP server

```bash
claude mcp add google-fonts -- uvx google-fonts-mcp
```

### Option B — Claude plugin

```bash
claude plugin install google-fonts
```

> Note: Requires Python/uvx for the MCP path. See the [GitHub README](https://github.com/sliday/google-fonts-skill) for full setup instructions.

## Invoke after install

- Skill name: `google-fonts-skill`
- MCP server name: `google-fonts`
- Triggers: "Google Fonts", "font pairing", "modular scale", "typography showcase"

## What it does

Provides intelligent Google Fonts selection across 1,923 fonts enriched with personality and mood metadata. Suggests from 73 battle-tested pairing combinations, generates ready-to-use CSS custom properties, Tailwind configuration snippets, and Google Fonts embed links. Offers 8 modular type scales and 100 pre-built typography showcases for instant design direction.

## When NOT to use

- Non-Google font sources (self-hosted, Adobe Fonts, etc.)
- Complete brand typography system needing token architecture — pair with `design-tokens-skill`
- Variable font fine-tuning at axis level — consult font-specific documentation

---
> Source: [podo/design-agent-skills](https://github.com/podo/design-agent-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
