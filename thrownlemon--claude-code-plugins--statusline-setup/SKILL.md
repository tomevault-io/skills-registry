---
name: statusline-setup
description: Auto-trigger skill for setting up and configuring the ultimate-statusline Use when this capability is needed.
metadata:
  author: thrownlemon
---

# Statusline Setup Skill

This skill auto-triggers when users want to set up, configure, or customize their Claude Code statusline.

## When This Triggers

- "setup statusline" / "install statusline"
- "configure statusline" / "statusline config"
- "change statusline theme"
- "customize statusline"
- "statusline widgets"
- "enable/disable statusline widget"

## What To Do

### For Setup/Install Requests

1. Check if the plugin is already installed by looking for `~/.claude/statusline-config.json`
2. If not installed, run the install command:

   ```bash
   /ultimate-statusline:install
   ```

3. Guide the user through initial configuration

### For Theme Changes

1. Show available themes:
   - default (balanced colors)
   - gruvbox (warm retro)
   - nord (arctic blues)
   - tokyo-night (neon cyberpunk)
   - rose-pine (soft pastels)
   - powerline (classic vim powerline)

2. Apply the requested theme:

   ```bash
   /ultimate-statusline:config set theme <theme-name>
   ```

### For Widget Configuration

1. If user wants to see available widgets:

   ```bash
   /ultimate-statusline:widgets
   ```

2. If user wants to enable/disable a widget:

   ```bash
   /ultimate-statusline:config set widgets.<widget_name>.enabled true/false
   ```

3. Common widget requests:
   - "show MCP status" → enable `mcp_status`
   - "show cost per hour" → enable `burn_rate`
   - "show daily cost" → enable `daily_cost`
   - "hide git info" → disable `git_branch` and `git_changes`
   - "show token usage" → enable `tokens_total` or `tokens_input`/`tokens_output`

### For Preview Requests

1. Show what the statusline will look like:

   ```bash
   /ultimate-statusline:preview
   ```

2. Preview with a specific theme:

   ```bash
   /ultimate-statusline:preview --theme <theme-name>
   ```

## Available Widgets

**Always mention these key widgets:**

| Widget | What it shows |
|--------|---------------|
| `model_emoji` | 🧠 Opus, 🎵 Sonnet, ⚡ Haiku |
| `context_percent` | Usage % with progress bar |
| `session_cost` | Current session cost |
| `burn_rate` | Cost per hour 🔥 |
| `mcp_status` | MCP server health 🟢/🔴 |
| `git_branch` | Current git branch |
| `daily_cost` | Today's spending (needs ccusage) |

## Response Pattern

After helping the user configure the statusline:

1. Summarize what was changed
2. Remind them to restart Claude Code if settings.json was modified
3. Suggest running `/ultimate-statusline:preview` to see the result

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thrownlemon) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
