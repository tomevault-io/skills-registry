---
name: add-mcp
description: Searches mcp.so marketplace for MCP servers and configures them for Claude, Codex, or Gemini. Use when user wants to add external tool integrations, find MCP servers, or configure model context protocol. Use when this capability is needed.
metadata:
  author: terrazul-ai
---

# Add MCP Server Skill

This skill helps you find and configure MCP (Model Context Protocol) servers from the mcp.so marketplace.

## When to Use

Activate this skill when the user wants to:
- Search for MCP servers
- Add external tool integrations
- Configure an MCP server for their project
- Find servers for specific capabilities (GitHub, databases, APIs)
- Set up MCP for Claude, Codex, or Gemini

## MCP Server Search Workflow

### 1. Search for Servers

Use WebSearch with the site filter:

```
site:mcp.so [search terms]
```

**Example searches**:
- `site:mcp.so github` - GitHub integrations
- `site:mcp.so database` - Database servers
- `site:mcp.so filesystem` - File system tools
- `site:mcp.so api` - API integrations

### 2. Present Results

Format results in a table:

| Server | Description | Category |
|--------|-------------|----------|
| @server/name | Brief description | Category |

Include:
- Server identifier
- Brief description
- Primary category/use case

### 3. Fetch Server Details

When user selects a server, use WebFetch:

```
WebFetch: https://mcp.so/server/[server-name]
```

Retrieve:
- Full description
- Installation requirements
- Configuration options
- Environment variables needed
- Usage examples

### 4. Choose Integration Mode

**Option A: Local Project**
- Config file: `.claude/settings.local.json`
- Best for: Project-specific servers
- Not committed to git (add to .gitignore)

**Option B: User Settings**
- Config file: `~/.claude/settings.json`
- Best for: Personal tools used across projects
- Available in all projects

**Option C: Package Template**
- Config file: `templates/claude/mcp_servers.json.hbs`
- Best for: Team-shared configurations
- Distributed with package

### 5. Generate Configuration

Generate the JSON configuration based on server docs:

```json
{
  "mcpServers": {
    "server-name": {
      "command": "npx",
      "args": ["-y", "@server/package"],
      "env": {
        "API_KEY": "${VARIABLE_NAME}"
      }
    }
  }
}
```

### 6. Apply Configuration

**For Local/User Settings**:
1. Read existing settings file
2. Merge new server configuration
3. Write updated settings
4. Remind user to restart Claude Code

**For Package**:
1. Update `mcp_servers.json.hbs` template
2. Run `tz apply --force` to regenerate

### 7. Post-Configuration

After adding:
1. Explain what tools the server provides
2. Show example usage
3. Note any environment variables to set
4. Remind to restart Claude Code
5. **If server requires credentials**, remind user to run `/setup-mcp` to configure environment variables

**Important:** If the MCP server requires API keys or credentials (indicated by `${VAR_NAME}` in the config), always remind the user:

```
Run /setup-mcp to configure the required credentials in your shell environment.
```

The `/setup-mcp` command provides an interactive wizard that:
- Identifies which environment variables are needed
- Guides through obtaining credentials for each service
- Adds exports to the appropriate shell profile (~/.zshenv, ~/.bashrc)
- Verifies the setup is working

## Reference Materials

For detailed information, consult:
- `reference.md` - Complete MCP configuration guide
- `examples.md` - Common server configurations
- `mcp-credentials.md` - Detailed credential setup instructions for known MCP servers

## Tips for Finding Servers

1. **Search by Use Case**: "github PR review", "postgres database"
2. **Check Requirements**: Some servers need API keys or local services
3. **Verify Security**: Only use trusted servers
4. **Read Documentation**: Understand what access you're granting

## Popular MCP Servers

### Development Tools
- **@anthropic/mcp-github** - GitHub integration (PRs, issues, repos)
- **@anthropic/mcp-filesystem** - File system access
- **@anthropic/mcp-git** - Git operations

### Databases
- **@anthropic/mcp-postgres** - PostgreSQL queries
- **@anthropic/mcp-sqlite** - SQLite database
- **@anthropic/mcp-redis** - Redis cache

### APIs & Services
- **@modelcontextprotocol/server-slack** - Slack integration
- **@anthropic/mcp-linear** - Linear issue tracking
- **@anthropic/mcp-notion** - Notion pages

### Documentation
- **@anthropic/mcp-context7** - Library documentation
- **@anthropic/mcp-exa** - Code search

## Example Invocations

Here are examples of how users might request MCP servers:

- "Add GitHub integration to my project"
- "Search for database MCP servers"
- "Configure the Context7 server"
- "What MCP servers are available for Slack?"
- "Set up MCP for PostgreSQL"

## Platform-Specific Configuration

### Claude Code
- Settings: `.claude/settings.local.json` or `~/.claude/settings.json`
- Package: `templates/claude/mcp_servers.json.hbs`

### Codex
- Settings: `~/.codex/config.toml`
- Format: TOML instead of JSON
- Package: `templates/codex/config.toml.hbs`

### Gemini
- Settings: `.gemini/settings.json`
- Package: `templates/gemini/settings.json.hbs`

## Security Considerations

1. **API Keys**: Store in environment variables, not in config files
2. **Permissions**: Only grant necessary access
3. **Trusted Sources**: Verify server reputation
4. **Data Access**: Understand what data servers can access
5. **Local vs Remote**: Prefer local servers when possible

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/terrazul-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
