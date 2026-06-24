---
name: claude-guide
description: Navigate Claude Code documentation and get quick answers about configuration, MCP, plugins, and best practices. Use when this capability is needed.
metadata:
  author: diegocconsolini
---

# Claude Guide Skill

Quick access to Claude Code documentation and best practices.

## Usage

```
/claude-guide                    # Show quick reference
/claude-guide mcp                # MCP server documentation
/claude-guide claudemd           # CLAUDE.md configuration
/claude-guide context            # Context optimization tips
/claude-guide session            # Session management
/claude-guide plugins            # Plugin documentation
/claude-guide examples           # List available examples
/claude-guide troubleshoot       # Troubleshooting guide
/claude-guide [search term]      # Search documentation
```

## Topics

| Topic | Description |
|-------|-------------|
| `mcp` | MCP servers: transports, scopes, authentication |
| `claudemd` | CLAUDE.md configuration and memory architecture |
| `context` | Context optimization and reduction strategies |
| `session` | Session management and workflow patterns |
| `plugins` | Plugin development and lifecycle |
| `examples` | Copy-paste examples for all categories |
| `troubleshoot` | Common issues and solutions |
| `platforms` | Platform-specific guides (macOS, Linux, WSL2, Windows) |
| `commands` | Claude Code command reference |

## Quick Reference

When invoked without arguments, shows:
- Essential commands
- Key configuration paths
- Common patterns
- Links to detailed docs

## Wiki

Full documentation at: https://github.com/diegocconsolini/ClaudeSkillCollection/wiki

## Agent

This skill uses the `claude-guide` agent to search and retrieve documentation.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/diegocconsolini) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
