---
name: advanced
description: | Use when this capability is needed.
metadata:
  author: timequity
---

# Advanced: Mastery

Build and publish your own Claude Code extensions.

## Curriculum

| # | Topic | Reference |
|---|-------|-----------|
| 1 | Custom Agents | `custom-agents.md` |
| 2 | MCP Servers | `mcp-servers.md` |
| 3 | Publishing | `publishing.md` |

## Teaching Pattern

```
1. CONCEPT   → Architecture and design
2. DEEP DIVE → Implementation details
3. BUILD     → Create a real one
4. PUBLISH   → Share with others
```

## Topic Details

### 1. Custom Agents (Subagents)

Key points:
- Agents = autonomous workers Claude can spawn
- Run in background, return results
- Have own tool permissions
- Great for code review, testing, research

Exercise: Build a code-reviewer agent

Reference: `custom-agents.md`

### 2. MCP Servers

Key points:
- Build your own integrations
- Python: FastMCP library
- TypeScript: @modelcontextprotocol/sdk
- Expose tools, resources, prompts

Exercise: Build MCP server for your API/service

Reference: `mcp-servers.md`

### 3. Publishing

Key points:
- Package your plugin properly
- Write compelling descriptions
- Set pricing strategy
- Marketing basics

Capstone: Publish your first plugin

Reference: `publishing.md`

## Capstone Project

Create and publish a complete plugin:

1. **Choose a niche** — What problem do you solve?
2. **Build 2-3 skills** — Core functionality
3. **Add an agent** — Automation capability
4. **Create commands** — User-friendly entry points
5. **Write help skill** — Documentation
6. **Publish** — Submit to marketplace

## Completion Criteria

User has completed Advanced when they can:

- [ ] Build a working agent with tool permissions
- [ ] Create an MCP server (even simple)
- [ ] Explain plugin marketplace workflow
- [ ] Have published (or ready to publish) a plugin

## What's Next?

After Advanced:
```
"You've mastered Claude Code!

Next steps:
- Build plugins for specific niches
- Contribute to open source MCP servers
- Help others learn (become a mentor)
- Build a business around plugins

The community needs your expertise.
What will you create?"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/timequity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
