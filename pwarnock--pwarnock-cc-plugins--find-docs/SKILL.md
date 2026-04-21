---
name: find-docs
description: Navigate Claude Code plugin development documentation and resources. Use when asked "where are the docs", "how do I learn plugin development", "find resources for plugins". Use when this capability is needed.
metadata:
  author: pwarnock
---

# Finding Plugin Development Documentation

This skill helps you navigate to the right documentation for Claude Code plugin development.

## Quick Reference

### Creating a Plugin

**Recommended:** Install the official `plugin-dev` plugin for always-current guidance:
```bash
/plugin install anthropic/plugin-dev
```

Then just describe what you want to build. Claude will use the appropriate skill:
- `plugin-dev:plugin-structure` - Plugin scaffolding, manifest format
- `plugin-dev:create-plugin` - End-to-end guided creation

**Alternative:** Use this marketplace's [create-plugin skill](../create-plugin/SKILL.md) for a workflow-based approach.

### Specific Development Tasks

| Task | Best Resource |
|------|---------------|
| Understanding plugin structure | `plugin-dev:plugin-structure` |
| Creating hooks | `plugin-dev:hook-development` |
| Adding MCP servers | `plugin-dev:mcp-integration` |
| Writing skills | `plugin-dev:skill-development` |
| Creating commands | `plugin-dev:command-development` |
| Building agents | `plugin-dev:agent-development` |
| Plugin settings | `plugin-dev:plugin-settings` |

### Official Documentation

- [Claude Code Plugins](https://docs.anthropic.com/en/docs/claude-code/plugins) - Official docs
- [Claude Code Hooks](https://docs.anthropic.com/en/docs/claude-code/hooks) - Hook reference
- [Claude Code MCP](https://docs.anthropic.com/en/docs/claude-code/mcp) - MCP integration

### MCP Resources

- [MCP Specification](https://spec.modelcontextprotocol.io/)
- [MCP SDK](https://github.com/modelcontextprotocol/sdk)
- [Official MCP Servers](https://github.com/modelcontextprotocol/servers)

### Marketplace-Specific Guides

These are unique to this marketplace:

| Guide | Use For |
|-------|---------|
| [RESOURCES.md](../../docs/RESOURCES.md) | Complete index of all documentation |
| [Marketplace Management](../../docs/marketplace-management.md) | Running your own marketplace |
| [Skill Curation](../../docs/skill-curation.md) | Wrapping external skills |
| [Quality Guidelines](../../docs/quality-guidelines.md) | Standards for plugin inclusion |

### Quick Reference Docs

These provide condensed overviews (for complete docs, use plugin-dev):

| Doc | Content |
|-----|---------|
| [Plugin Development](../../docs/plugin-development.md) | Plugin structure quick reference |
| [MCP Integration](../../docs/mcp-integration.md) | MCP configuration quick reference |

## Decision Tree

### "I want to build a plugin"
1. Install `plugin-dev`: `/plugin install anthropic/plugin-dev`
2. Describe your plugin idea
3. Claude will guide you through creation

### "I need to understand hooks"
→ Use `plugin-dev:hook-development`

### "I want to add an MCP server to my plugin"
→ Use `plugin-dev:mcp-integration`

### "I want to run a marketplace"
→ Read [Marketplace Management](../../docs/marketplace-management.md)

### "I want to wrap external skills"
→ Read [Skill Curation](../../docs/skill-curation.md)

### "I want to submit a plugin to this marketplace"
→ Review [Quality Guidelines](../../docs/quality-guidelines.md), then open an issue

## Community Resources

- [Everything Claude Code](https://github.com/affaan-m/everything-claude-code) - Community knowledge base
- [Context7 Docs](https://context7.com) - Library documentation via MCP

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pwarnock) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
