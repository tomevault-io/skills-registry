---
name: windsurf-mcp-integration
description: | Use when this capability is needed.
metadata:
  author: ZenProjectGit
---
# Windsurf Mcp Integration

## Overview

This skill enables integration of MCP (Model Context Protocol) servers with Windsurf, extending Cascade's capabilities with external tools and services. MCP allows Cascade to interact with databases, filesystems, APIs, and custom tools through a standardized protocol.

## Prerequisites

- Windsurf IDE with MCP support enabled
- Node.js 18+ or Python 3.10+ for MCP servers
- MCP server packages installed (npm or pip)
- Network access for remote MCP servers
- Understanding of MCP protocol basics
- Admin permissions for server configuration

## Instructions

1. **Enable MCP Servers**
2. **Configure Tools**
3. **Set Up Authentication**
4. **Test Integration**
5. **Deploy to Team**

See `${CLAUDE_SKILL_DIR}/references/implementation.md` for detailed implementation guide.

## Output

- Configured MCP servers accessible via Cascade
- Tool registry with all available operations
- Permission matrix for access control
- Audit logs for tool invocations

## Error Handling

See `${CLAUDE_SKILL_DIR}/references/errors.md` for comprehensive error handling.

## Examples

See `${CLAUDE_SKILL_DIR}/references/examples.md` for detailed examples.

## Resources

- [MCP Protocol Specification](https://modelcontextprotocol.io/docs)
- [Windsurf MCP Guide](https://docs.windsurf.ai/features/mcp)
- [MCP Server Development](https://modelcontextprotocol.io/docs/servers)

---
> Source: [ZenProjectGit/claude-code-plugins-plus-skills](https://github.com/ZenProjectGit/claude-code-plugins-plus-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
