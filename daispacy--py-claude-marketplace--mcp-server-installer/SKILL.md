---
name: mcp-server-installer
description: Add MCP servers to Claude Code configuration at user level (~/.claude). Supports stdio, HTTP, and SSE transports with environment variable prompting. Use when "add mcp server", "install mcp", "configure mcp server", "new mcp", or "setup mcp server". Use when this capability is needed.
metadata:
  author: daispacy
---

# MCP Server Installer

Autonomously add MCP (Model Context Protocol) servers to Claude Code configuration at the user level (`~/.claude/mcp.json`).

## When to Activate

- "add mcp server"
- "install mcp", "configure mcp"
- "new mcp server", "setup mcp"
- "add [server-name] mcp"
- User provides MCP server configuration JSON

## Process

### Step 1: Gather Server Information

Ask user for:

1. **Server name** (e.g., "mobile-mcp-server")
   - Must be lowercase with hyphens
   - Will be used as the key in mcpServers object

2. **Transport type**:
   - `stdio` - Local command execution (most common)
   - `http` - Remote HTTP server
   - `sse` - Server-Sent Events (deprecated but supported)

3. **Based on transport type:**

   **For stdio:**
   - Command (e.g., "npx", "node", "/usr/local/bin/server")
   - Arguments array (e.g., `["@daipham/mobile-mcp-server@latest"]`)
   - Environment variables (if needed)

   **For http:**
   - URL (e.g., "https://api.example.com/mcp")
   - Headers (optional, e.g., `{"Authorization": "Bearer ${API_KEY}"}`)

   **For sse:**
   - URL (e.g., "https://api.example.com/sse")
   - Headers (optional)

### Step 2: Collect Environment Variables

**Always ask user**: "Does this MCP server require any environment variables?"

If yes, for each variable:
- Variable name (e.g., "API_KEY", "DATABASE_URL")
- Description/purpose
- Whether it's required or optional
- Default value (if optional)

**Document in output**: List all required environment variables with instructions on where to set them.

### Step 3: Read Existing Configuration

Check for existing `.mcp.json` at user level:

```bash
cat ~/.claude/mcp.json
```

If file doesn't exist, create new structure. If exists, parse and merge.

### Step 4: Generate Configuration

Use templates from `templates.md` to generate the server configuration based on transport type.

Apply environment variable syntax:
- `${VAR}` - Required variable (will error if not set)
- `${VAR:-default}` - Optional variable with default value

### Step 5: Update Configuration File

**Merge strategy:**
- Preserve existing servers
- Add new server to `mcpServers` object
- Maintain JSON formatting (2-space indent)
- Validate JSON before writing

Write to `~/.claude/mcp.json`.

### Step 6: Create Environment File Reference

If environment variables are needed, inform user:

```
⚠️ Environment Variables Required:

Set these in your shell profile (~/.zshrc or ~/.bashrc):

export VAR_NAME="value"
export ANOTHER_VAR="value"

Or create a project-specific .env file and reference it:
"envFile": "${workspaceFolder}/.env"
```

### Step 7: Validate and Test

Run validation:

```bash
claude mcp list
```

Show the newly added server in the output.

## Output Format

```markdown
✅ MCP Server Added: [server-name]

📁 Configuration: ~/.claude/mcp.json

🔧 Transport: [stdio/http/sse]
📦 Command: [command with args] (if stdio)
🌐 URL: [url] (if http/sse)

📋 Configuration Added:
```json
{
  "server-name": {
    // configuration here
  }
}
```

⚠️ Environment Variables Required:
- VAR_NAME: [description]
- ANOTHER_VAR: [description]

💡 Set environment variables:
1. Add to ~/.zshrc or ~/.bashrc:
   export VAR_NAME="your-value"

2. Or use project .env file:
   "envFile": "${workspaceFolder}/.env"

✅ Restart Claude Code to activate the new MCP server.

🧪 Test with: claude mcp list
```

## Error Handling

- **Invalid JSON**: Show parsing error, ask user to verify configuration
- **Duplicate server name**: Ask if user wants to overwrite existing server
- **Missing required fields**: Prompt for missing information
- **File permission errors**: Suggest checking ~/.claude directory permissions

## Important Notes

1. **User-level scope**: Servers added at `~/.claude/mcp.json` are available across all projects
2. **Environment variables**: Always prompt for environment variables - many MCP servers require API keys or configuration
3. **Restart required**: Claude Code must be restarted after adding MCP servers
4. **Validation**: Use `claude mcp list` to verify the server was added successfully
5. **Security**: Never log or display actual environment variable values

## Reference

See `templates.md` for configuration templates for each transport type.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/daispacy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
