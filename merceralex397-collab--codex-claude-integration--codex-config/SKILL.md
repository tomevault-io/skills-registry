---
name: codex-config
description: View and modify Codex plugin settings for this session Use when this capability is needed.
metadata:
  author: merceralex397-collab
---

# Codex Configuration

Manage Codex plugin settings. View current configuration, override settings for the session, or edit the settings file.

## Usage

- `/codex-config` - Show current settings
- `/codex-config <key>=<value>` - Set value for this session
- `/codex-config reset` - Reset to file defaults
- `/codex-config edit` - Open settings file for editing
- `/codex-config create` - Create settings file from template

## Examples

```
/codex-config                             # Show all settings
/codex-config model=gpt-5.1-codex-max     # Override model
/codex-config reasoning_effort=high       # Override reasoning
/codex-config gates.enabled=test,lint     # Set active gates
/codex-config reset                       # Reset session overrides
/codex-config create                      # Create settings file
```

## Implementation

### Show Current Settings (no arguments)

1. Check if user settings file exists:
   ```bash
   python scripts/settings_loader.py --check
   ```

2. Load and display current effective settings:
   ```bash
   python scripts/settings_loader.py --json
   ```

3. Format output as a readable summary:
   ```
   Codex Plugin Settings
   =====================
   Settings file: ~/.claude/codex-settings.toml [exists/not found]

   Defaults:
     model: gpt-5.2-codex
     reasoning_effort: medium
     verbosity: normal

   Quality Gates:
     enabled: test, lint
     max_retries: 3
     auto_iterate: true

   Profiles:
     quick:    gpt-5.1-codex-mini (low reasoning)
     standard: gpt-5.2-codex (medium reasoning)
     deep:     gpt-5.1-codex-max (high reasoning)
     review:   gpt-5.2 (medium reasoning)
   ```

### Set Session Override (key=value)

Parse the argument and explain that the override applies to the current session only.
Note: Session overrides are held in conversation context and need to be passed to subsequent /codex calls.

Display confirmation:
```
Session override set:
  model = gpt-5.1-codex-max

This override applies to the current session only.
To make it permanent, run: /codex-config edit
```

### Reset Session Overrides

Clear any session overrides and confirm:
```
Session overrides cleared.
Using settings from: ~/.claude/codex-settings.toml
```

### Create Settings File

1. Check if settings file already exists
2. If not, copy template:
   ```bash
   cp <plugin_dir>/config/codex-settings.toml ~/.claude/codex-settings.toml
   ```
3. Confirm:
   ```
   Created settings file: ~/.claude/codex-settings.toml
   Edit this file to customize your Codex plugin settings.
   ```

### Edit Settings File

1. Show the settings file path
2. Display current contents using Read tool
3. User can then modify directly or request changes

## Settings Reference

### defaults

| Setting | Values | Description |
|---------|--------|-------------|
| `model` | gpt-5.2-codex, gpt-5.1-codex-max, gpt-5.1-codex-mini, gpt-5.2 | Default model |
| `reasoning_effort` | low, medium, high, xhigh | Default reasoning level |
| `verbosity` | quiet, normal, verbose | Output detail level |

### gates

| Setting | Values | Description |
|---------|--------|-------------|
| `enabled` | Array of: test, lint, typecheck, security | Which gates to run |
| `max_retries` | 1-10 | Retries per failing gate |
| `auto_iterate` | true/false | Continue after gate failure |

### profiles

Each profile can override:
- `model`: Which Codex model to use
- `reasoning_effort`: How much reasoning to apply
- `timeout`: Max seconds to wait

## Settings File Location

```
~/.claude/codex-settings.toml
```

This file is user-specific and is not overwritten by plugin updates.

## Flags Available on /codex Commands

These flags override settings for a single call:

| Flag | Effect |
|------|--------|
| `--model=<model>` | Override model |
| `--reasoning=<level>` | Override reasoning effort |
| `--profile=<name>` | Use specific profile |
| `--no-iterate` | Skip quality gates |
| `--verbose` | Show detailed output |
| `--quiet` | Minimal output |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/merceralex397-collab) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
