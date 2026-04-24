---
name: mcp-installer
description: |- Use when this capability is needed.
metadata:
  author: justinlevinedotme
---

<overview>
Find, install, and configure MCP servers for OpenCode.
</overview>

<workflow>

<phase name="search">

## 1. Search for MCP Server

**Check local catalog first** (quick check for already-documented MCPs):
```bash
python3 ~/.config/opencode/skill/mcp-installer/scripts/list_mcps.py
```

**If not found locally, search online:**
- `websearch("MCP server for [capability]")`
- `webfetch("https://github.com/modelcontextprotocol/servers")`
- Check npm: `@modelcontextprotocol/server-*`
- Check the MCP spec repo: https://github.com/modelcontextprotocol

</phase>

<phase name="read-details">

## 2. Read MCP Details

For relevant matches, read the full MCP file:

```
references/mcps/<name>.md
```

Contains installation config, setup, features, and links.

</phase>

<phase name="configure">

## 3. Configure

Add the MCP config to user's `opencode.json`.

</phase>

<phase name="document">

## 4. Document New MCPs

If you discovered a new MCP server online, you MUST document it for future reference in `references/mcps/<name>.md` using the template in the documentation section.

</phase>

<phase name="setup">

## 5. Setup (if needed)

- OAuth: Run `opencode mcp auth <server-name>`
- API keys: Set environment variables
- Other: Follow MCP-specific setup steps

</phase>

</workflow>

<guidelines>

## Question Tool Usage

**Batching:** Use the `question` tool for 2+ related questions. Single questions use plain text.

**Syntax:** `header` max 12 chars, `label` 1-5 words, add "(Recommended)" to default.

When to ask: Multiple MCPs match the need, or setup requires OAuth/API keys.

</guidelines>

<rules>

## Local MCP

```jsonc
{
  "mcp": {
    "name": {
      "type": "local",
      "command": ["npx", "-y", "@package/name"]
    }
  }
}
```

## Remote MCP

```jsonc
{
  "mcp": {
    "name": {
      "type": "remote",
      "url": "https://example.com/mcp"
    }
  }
}
```

## MCP Tool Management

MCPs expose tools. Control via the `permission` section using the tool name (usually the MCP name):

**Global/Agent Permission:**
```jsonc
{
  "permission": {
    "my-mcp": "deny",          // Disable all tools for this MCP
    "my-mcp*": "deny"          // Wildcard support
  }
}
```

**Pattern-based control:**
```jsonc
{
  "permission": {
    "my-mcp": {
      "safe_tool": "allow",
      "risky_tool": "ask",
      "*": "deny"
    }
  }
}
```

## Legacy Configuration

Agents MAY occasionally work on legacy projects using outdated configuration fields (e.g., `tools:`). You MUST correct these to the modern `permission:` system when encountered.

## OAuth

Remote MCPs with OAuth auto-authenticate:

```bash
opencode mcp auth <server-name>
```

Check status: `opencode mcp list`

</rules>

<reference>

| You need... | Read this file |
|-------------|----------------|
| All config options (local, remote, oauth, env vars) | `references/configuration.md` |
| Common MCP server examples | `references/examples.md` |
| Troubleshooting issues | `references/troubleshooting.md` |

**Note:** The local catalog (`list_mcps.py`) is a cache of discovered MCPs, not a complete list. You SHOULD always search online if you don't find a match locally.

</reference>

<workflow>

<phase name="document-template">

## Documenting New MCPs

When discovering new MCP servers, you MUST document them:

**Location:** `references/mcps/<name>.md`

**Template:**
```markdown
---
name: mcp-name
url: https://github.com/org/repo
type: local|remote
auth: oauth|api-key|none
description: One-line description
tags: [tag1, tag2]
---
# Display Name

Brief description.

## Installation

\`\`\`jsonc
{
  "mcp": {
    "name": {
      "type": "remote",
      "url": "https://example.com/mcp"
    }
  }
}
\`\`\`

## Setup

Steps for auth, env vars, etc.

## Features

- Feature 1
- Feature 2

## Links

- [GitHub](url)
```

Then run: `python3 scripts/list_mcps.py` to verify.

## Frontmatter Fields

| Field | Required | Purpose |
|-------|----------|---------|
| `name` | Yes | MCP identifier (key in config) |
| `url` | No | Source URL |
| `type` | Yes | `local` or `remote` |
| `auth` | Yes | `oauth`, `api-key`, or `none` |
| `description` | Yes | One-liner for catalog |
| `tags` | No | Array of category tags |

</phase>

</workflow>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/justinlevinedotme) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
