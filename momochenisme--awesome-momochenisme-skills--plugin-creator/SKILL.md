---
name: plugin-creator
description: Guide for creating Claude Code plugins, including directory structure, manifest configuration, component types, and initialization scripts. Use when users want to create, build, or understand Claude Code plugins. Use when this capability is needed.
metadata:
  author: momochenisme
---

# Plugin Creator

## What is a Plugin?

A plugin is a **distributable package format** that bundles skills, hooks, MCP servers, and other components into an installable unit for sharing across projects or teams.

### When to Use Plugin vs Standalone?

| Use Case | Recommended Approach |
|----------|---------------------|
| Single project only | Place directly in `.claude/` directory |
| Share across multiple projects | Create a Plugin |
| Distribute to other users | Create a Plugin |
| Internal project automation | Place directly in `.claude/` directory |

## Quick Start

### Using the Initialization Script

```bash
# Create new plugin in current directory
python skills/plugin-creator/scripts/init_plugin.py my-awesome-plugin

# Specify output path
python skills/plugin-creator/scripts/init_plugin.py my-plugin --output ~/plugins
```

### Manual Creation

1. Create plugin directory structure:
   ```
   my-plugin/
   ├── .claude-plugin/
   │   └── plugin.json      # Plugin manifest (required)
   ├── commands/            # User-invocable skills
   ├── skills/              # Agent skills
   ├── hooks/               # Hook configurations
   └── README.md
   ```

2. Create `plugin.json` manifest:
   ```json
   {
     "name": "my-plugin",
     "description": "Plugin description",
     "version": "1.0.0",
     "author": "Your Name"
   }
   ```

## Testing Plugins

Use the `--plugin-dir` flag to load and test your plugin:

```bash
# Test local plugin
claude --plugin-dir ./my-plugin

# Test multiple plugins
claude --plugin-dir ./plugin-a --plugin-dir ./plugin-b
```

## Component Types Overview

Plugins can contain the following components:

| Component Type | Directory | Purpose |
|----------------|-----------|---------|
| Commands | `commands/` | User-invocable slash commands |
| Skills | `skills/` | Agent auto-used skills |
| Hooks | `hooks/` | Event-triggered automation scripts |
| MCP Servers | In manifest | Extend Claude's tool capabilities |
| LSP Servers | In manifest | Code intelligence features |

## Reference Documentation

For detailed information:

- [Plugin Directory Structure](references/plugin-structure.md) - Manifest schema, directory layout, common errors
- [Component Types Guide](references/component-types.md) - Detailed usage for each component type

## FAQ

### Q: Why isn't my skill being loaded?

Check the following:
1. `plugin.json` is inside `.claude-plugin/` directory
2. Skills are in plugin root's `skills/` or `commands/`, **not** inside `.claude-plugin/`
3. Manifest JSON format is valid

### Q: How to debug plugin loading issues?

```bash
# Use verbose mode to see loading process
claude --plugin-dir ./my-plugin -v
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/momochenisme) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
