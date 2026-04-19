---
name: convex-mcp
description: Queries Convex database using the Convex MCP server. Use when checking data in the database, listing tables, running queries, or inspecting Convex deployments. Use when this capability is needed.
metadata:
  author: matixlol
---

# Convex MCP Server

Use the Convex MCP tools to interact with the database.

## Workflow

1. **Get deployment**: Run `status` first to get the deployment selector
2. **Explore schema**: Use `tables` to see available tables and their schemas
3. **Browse data**: Use `data` to paginate through documents in a table
4. **Custom queries**: Use `runOneoffQuery` for read-only JavaScript queries

## Available Tools

| Tool | Purpose |
|------|---------|
| `status` | Get deployment selector (run first) |
| `tables` | List tables with schemas |
| `data` | Paginate through table documents |
| `runOneoffQuery` | Execute read-only JS queries |
| `functionSpec` | Get metadata about deployed functions |
| `run` | Execute deployed Convex functions |
| `logs` | Fetch recent function execution logs |

## Example Queries

```javascript
// runOneoffQuery: Count documents in a table
const count = await db.query("users").collect();
return count.length;

// runOneoffQuery: Filter documents
const active = await db.query("users")
  .filter(q => q.eq(q.field("status"), "active"))
  .collect();
return active;
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/matixlol) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
