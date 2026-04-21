---
name: mcp-server-generator
description: Logic for generating and configuring Model Context Protocol (MCP) servers to extend agent capabilities. Use when this capability is needed.
metadata:
  author: kuwa72
---
# Skill: MCP Server Generator

This skill teaches an agent how to build, configure, and integrate MCP servers into the current project or as standalone services.

## 🛠️ Implementation Steps
1.  **Define Protocol**: Identify which MCP features are needed (Resources, Prompts, Tools).
2.  **Scaffold Server**: Use `npx @modelcontextprotocol/create-server` or manual setup with `sdk`.
3.  **Implement Logic**: Add functions for the specific domain (e.g., database access, API integration).
4.  **Registration**: Add the server to the agent configuration (`st-agent-config.json` or equivalent).

## 📄 Example: Simple SQLite MCP
```typescript
import { Server } from "@modelcontextprotocol/sdk/server/index.js";
// Implementation details...
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kuwa72) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
