---
name: mcp-server
description: Governs the noter local MCP server (F6). Invoke when implementing, debugging, or extending the MCP server at mcp-server/. Covers tool contract, path resolution, build process, and Claude Code / Cursor configuration. Use when this capability is needed.
metadata:
  author: euroconic
---

# MCP Server Skill

## Governing Principle

The MCP server is a read-only local bridge. It never writes to disk, never makes network calls, and never has access to anything outside `~/Documents/noter/History/` and the Electron userData workspace file. Privacy is the product.

---

## Architecture

```
mcp-server/
  src/index.ts       - Server entry point (TypeScript, ESM)
  package.json       - Standalone package, separate from Electron app
  tsconfig.json      - Targets Node16 module resolution
  dist/index.js      - Compiled output (run this)
```

**Transport:** StdioServerTransport — standard for local MCP servers. Claude Code and Cursor connect via stdin/stdout.

**Data sources:**
- `~/Documents/noter/History/YYYY-MM-DD.md` — timestamped voice transcripts
- `~/Library/Application Support/noter/workspace.json` — BlockNote workspace tabs (tries two paths: prod + dev)

---

## Tool Contract

| Tool | Required args | Optional args | Returns |
|---|---|---|---|
| `list_meetings` | — | `date: string` | List of date files with entry counts |
| `get_transcript` | `date: string` | — | Raw transcript text for that date |
| `get_notes` | `date: string` | `title: string` | Workspace tab content as plain text |
| `get_today_context` | — | — | Combined today transcript + notes summary |

### Date format
All `date` arguments use `YYYY-MM-DD` format. The server never infers timezone — dates are derived from file system names.

---

## Build & Run

```bash
cd mcp-server
npm install
npm run build        # tsc → dist/index.js
npm start            # runs dist/index.js via StdioServerTransport
```

---

## Claude Code Configuration

Add to `~/.claude/settings.json` (or project-level `.claude/settings.json`):

```json
{
  "mcpServers": {
    "noter": {
      "command": "node",
      "args": ["/Users/<you>/Documents/home-automation/noter/mcp-server/dist/index.js"]
    }
  }
}
```

After adding, restart Claude Code. Verify with `/mcp` command — `noter` should appear as connected.

---

## Cursor Configuration

Add to `.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "noter": {
      "command": "node",
      "args": ["/Users/<you>/Documents/home-automation/noter/mcp-server/dist/index.js"]
    }
  }
}
```

---

## Adding New Tools

1. Add handler function `handleNewTool(args)` in `src/index.ts`.
2. Register in `ListToolsRequestSchema` handler — name, description, inputSchema.
3. Add case in `CallToolRequestSchema` switch.
4. Rebuild: `npm run build`.
5. Restart Claude Code to reload the server.

**Tool naming rule:** All tools must be readable English verbs (`get_`, `list_`, `search_`). No abbreviations.

---

## Security Constraints

- All file reads use `fs.readFileSync` — synchronous, no async file handles left open.
- Path construction uses `path.join` with hardcoded base directories. No user-supplied paths are joined.
- No `eval`, no `exec`, no network calls.
- The server process has access to the full filesystem via Node.js — the sandbox is the trust boundary, not the code. Do not add tools that accept arbitrary file paths.

---

## What This Skill Does NOT Cover

- Google Calendar sync (F7 — see PRD backlog)
- Writing back to History or workspace files
- Authentication or OAuth (this server requires zero auth — it's local-only)
- Auto-starting with the Electron app (Q6 open — deferred to v2)

---
> Source: [euroconic/noter](https://github.com/euroconic/noter) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
