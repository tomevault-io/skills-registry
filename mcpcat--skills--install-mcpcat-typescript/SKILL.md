---
name: install-mcpcat-typescript
description: Use when integrating MCPCat analytics into a TypeScript MCP server, adding mcpcat to an existing TypeScript MCP project, setting up MCP server usage tracking, or when the user mentions mcpcat, MCPCat, or MCP analytics in a TypeScript context
metadata:
  author: mcpcat
---

# Installing MCPCat Analytics SDK

## Overview

MCPCat is a one-line analytics integration for MCP servers. Call `mcpcat.track(server, projectId)` to capture tool usage patterns, user intent, and session data. All MCPCat debug output goes to `~/mcpcat.log` (never stdout/stderr, so STDIO transport is preserved).

## When to Use

- User wants to add analytics or usage tracking to their MCP server
- User mentions "mcpcat", "MCPCat", or MCP server observability
- User wants to understand how their MCP tools are being used

## Quick Reference

| Item | Value |
|------|-------|
| Package | `mcpcat` |
| Import | `import * as mcpcat from "mcpcat"` |
| Core call | `mcpcat.track(server, projectId)` |
| Peer dep | `@modelcontextprotocol/sdk >= 1.11` |
| MCPCat log file | `~/mcpcat.log` |
| Project ID env var | `MCPCAT_PROJECT_ID` |
| Debug env var | `MCPCAT_DEBUG_MODE` |
| Defaults | All features enabled (context injection, tracing, report-missing tool) |

## Implementation Checklist

Follow these steps in order. Do NOT skip or reorder.

### 0. Get the MCPCat project ID

If the user did not provide a MCPCat project ID (a string like `"proj_abc123xyz"` from mcpcat.io), **ASK them for it before proceeding**. Say something like:

> "To set up MCPCat, I'll need your project ID from mcpcat.io. It looks like `proj_abc123xyz`. Do you have one? You can create a free account at https://mcpcat.io to get one."

- If they provide one: hardcode it as the default in the env var fallback, e.g. `process.env.MCPCAT_PROJECT_ID ?? "proj_their_id"`
- Do NOT skip this step or assume a project ID

### 1. Read the user's existing MCP server code

Before making ANY changes, read the server file(s) and identify these three landmarks:

- **Server creation**: `new McpServer(...)` (high-level) or `new Server(...)` (low-level)
- **Tool registrations**: `server.registerTool(...)`, `server.tool(...)`, or `server.setRequestHandler(CallToolRequestSchema, ...)`
- **Transport connection**: `server.connect(transport)` — usually inside an async `main()` function

### 2. Check peer dependency

Look at `package.json` and verify `@modelcontextprotocol/sdk` version is `>= 1.11`. If older, tell the user they must upgrade first.

### 3. Install the mcpcat package

Detect the package manager and install:

| Indicator | Command |
|-----------|---------|
| `pnpm-lock.yaml` | `pnpm add mcpcat` |
| `yarn.lock` | `yarn add mcpcat` |
| `bun.lockb` or `bun.lock` | `bun add mcpcat` |
| `package-lock.json` | `npm install mcpcat` |
| Otherwise | Check the project's README and other `.md` files for dependency management instructions, then install accordingly |

### 4. Add the import

Add near the top of the server file, alongside existing MCP SDK imports:

```typescript
import * as mcpcat from "mcpcat";
```

Use the namespace import (`import * as mcpcat`), not named import (`import { track }`).

### 5. Add the track() call

**CRITICAL: The `track()` function signature is:**

```typescript
mcpcat.track(server, projectId, options?)
```

- **1st arg**: The MCP server instance (both `McpServer` and `Server` work)
- **2nd arg**: Project ID **string** (e.g. `"proj_abc123xyz"`)
- **3rd arg**: Options object (optional — all defaults are good, do NOT pass unless customizing)

**WRONG:** `track(server, { projectId })` — this passes an options object as the 2nd arg.
**RIGHT:** `track(server, projectId)` — projectId is the 2nd positional argument.

**Placement:** Insert AFTER server creation and BEFORE `server.connect(transport)`. Place it at the module level alongside tool registrations, NOT inside the async `main()` function.

**Always call track()** with the project ID from the env var, falling back to the user's hardcoded ID:

```typescript
mcpcat.track(server, process.env.MCPCAT_PROJECT_ID ?? "proj_their_id");
```

### 6. Add debug logging

MCPCat writes all its internal debug output to `~/mcpcat.log`. Add a single stderr line (controlled by env var) so the user knows tracking is active:

```typescript
if (process.env.MCPCAT_DEBUG_MODE) {
  console.error("[mcpcat] Analytics enabled. Debug log: ~/mcpcat.log");
}
```

Use `console.error`, NEVER `console.log` — stdout is the MCP protocol channel for STDIO transport.

Keep it to this single line. Do NOT create a debug utility function or add multiple debug log points.

### 7. Verify

After making changes, confirm:
- Server still starts and responds to tool calls
- `~/mcpcat.log` exists and contains initialization entries
- No MCPCat output appears on stdout

## Before and After

### BEFORE

```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";

const server = new McpServer({ name: "my-server", version: "1.0.0" });

server.registerTool("greet", {
  description: "Greets a user",
  inputSchema: { name: { type: "string" } },
}, async (args) => ({
  content: [{ type: "text", text: "Hello, " + args.name + "!" }],
}));

async function main() {
  const transport = new StdioServerTransport();
  await server.connect(transport);
}

main().catch(console.error);
```

### AFTER

```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import * as mcpcat from "mcpcat";

const server = new McpServer({ name: "my-server", version: "1.0.0" });

server.registerTool("greet", {
  description: "Greets a user",
  inputSchema: { name: { type: "string" } },
}, async (args) => ({
  content: [{ type: "text", text: "Hello, " + args.name + "!" }],
}));

mcpcat.track(server, process.env.MCPCAT_PROJECT_ID ?? "proj_abc123xyz");

if (process.env.MCPCAT_DEBUG_MODE) {
  console.error("[mcpcat] Analytics enabled. Debug log: ~/mcpcat.log");
}

async function main() {
  const transport = new StdioServerTransport();
  await server.connect(transport);
}

main().catch(console.error);
```

Changes: one import, one `track()` call, one debug log block.

## Low-Level Server Pattern

For servers using the `Server` class directly:

```typescript
import { Server } from "@modelcontextprotocol/sdk/server/index.js";
import * as mcpcat from "mcpcat";

const server = new Server(
  { name: "my-server", version: "1.0.0" },
  { capabilities: { tools: {} } }
);

// ... setRequestHandler calls ...

mcpcat.track(server, process.env.MCPCAT_PROJECT_ID ?? "proj_abc123xyz");

if (process.env.MCPCAT_DEBUG_MODE) {
  console.error("[mcpcat] Analytics enabled. Debug log: ~/mcpcat.log");
}

// Then connect transport as usual
```

## Common Mistakes

| Mistake | Why it fails | Fix |
|---------|-------------|-----|
| `track(server, { projectId })` | Options object passed as 2nd arg instead of projectId string | `track(server, projectId)` — projectId is the 2nd positional arg |
| `if (projectId) { track(...) }` | Skips tracking if env var is unset, defeating the hardcoded fallback | Always call `track(server, process.env.MCPCAT_PROJECT_ID ?? "proj_abc123xyz")` |
| `track()` after `server.connect()` | Transport starts before MCPCat intercepts handlers | Move `track()` before `connect()` |
| `track()` called twice | MCPCat detects and skips, but indicates logic error | Call `track()` exactly once per server |
| `console.log` for debug output | Breaks STDIO transport (stdout is the MCP channel) | Use `console.error` |
| Creating a debugLog utility | Over-engineers a one-liner | Single `if (process.env.MCPCAT_DEBUG_MODE) console.error(...)` |
| Expecting console output from MCPCat | MCPCat logs to file, not console | Check `~/mcpcat.log` |
| `@modelcontextprotocol/sdk` < 1.11 | MCPCat requires >= 1.11 | Upgrade: `npm install @modelcontextprotocol/sdk@latest` |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mcpcat) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
