---
name: opensoulmd
description: Search, summon, and possess your agent with SOUL.md personality files from the OpenSOUL.md registry Use when this capability is needed.
metadata:
  author: danielliuzy
---

You can manage your agent's personality by possessing it with SOUL.md files from the OpenSOUL.md registry.

## Available actions

### Possess — change soul

When the user asks to change personality/soul:

1. Run `soul possess <name> --yes` — this auto-summons from the registry if the soul isn't cached locally.

You can also possess from a local file path: `soul possess /path/to/SOUL.md --yes`

Use `--dry-run` to preview what would happen without writing anything.

### Exorcise — restore original

If the user wants to go back to their original personality: `soul exorcise`

This restores the backed-up SOUL.md from before the first possession.

### Search souls

To search the registry: `soul search <query> --no-interactive`

Sorting options:

- `--top` — sort by highest-rated
- `--popular` — sort by most downloaded

To show all available souls: `soul search --top --no-interactive`

### Summon — download without possessing

To download a soul to local cache without activating it: `soul summon <label>`

The user can activate it later with `soul possess <name>`.

### List cached souls

To show locally cached souls: `soul list`

Supports pagination with `--page <n>` and `--per-page <n>`.

### Banish — remove from cache

To remove a soul from the local cache: `soul banish <name>`

### Status

To check what soul is currently loaded: `soul status`

Shows the SOUL.md path, possession state (original or possessed), and backup status.

### Path — show or set SOUL.md location

To show the current SOUL.md path: `soul path`

To set a new path: `soul path /path/to/SOUL.md`

To show or set the OpenClaw skills directory: `soul path --skills` or `soul path /path/to/skills --skills`

### Config

To get or set CLI configuration values:

- `soul config get <key>`
- `soul config set <key> <value>`

### Install / Uninstall skill

To install the OpenSoul skill into OpenClaw: `soul install`

To remove it: `soul uninstall`

## Important notes

- Always use `--no-interactive` with `soul search` since you cannot use interactive TUI controls.
- Always use `--yes` with `soul possess` to skip the confirmation prompt.
- `soul possess` auto-summons from the registry if the soul isn't in the local cache — you don't need to summon first.
- After possessing a soul, let the user know they can use `soul exorcise` to restore their original personality.
- The soul takes effect on the next conversation — the current conversation is not affected.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/danielliuzy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
