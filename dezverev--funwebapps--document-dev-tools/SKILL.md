---
name: document-dev-tools
description: Documents available MCP servers, tools, skills, and agents into DEVELOPMENTTOOLS.md. Use when new MCP servers or tools are added to Cursor, when skills or agents are created or modified, when the user asks to document available tools, or when setting up development environment documentation. Use when this capability is needed.
metadata:
  author: dezverev
---

# Document Development Tools

This skill scans and documents all available MCP servers, tools, skills, and agents into `.cursor/DEVELOPMENTTOOLS.md`.

## When to Use

- New MCP servers or tools are added/removed from Cursor
- Skills or agents are created, modified, or removed
- User explicitly requests tool documentation
- Setting up or updating development environment docs

## Workflow

### Step 1: Gather MCP Server Information

1. List all MCP server folders in the mcps directory
2. For each server, list all tools in its `tools/` subdirectory
3. Note any server-specific usage instructions

**MCP tools location pattern:**
```
{mcps_folder}/{server-name}/tools/*.json
```

### Step 2: Gather Skills Information

Check for skills in both locations:
- Personal: `~/.cursor/skills/*/SKILL.md`
- Project: `.cursor/skills/*/SKILL.md`

For each skill, extract:
- Name (from frontmatter)
- Description (from frontmatter)
- Trigger scenarios

### Step 3: Document Available Agents

Document the built-in agent types:
- `generalPurpose` - Complex multi-step tasks
- `explore` - Fast codebase exploration
- `shell` - Command execution specialist

### Step 4: Generate DEVELOPMENTTOOLS.md

Create/update `.cursor/DEVELOPMENTTOOLS.md` with this structure:

```markdown
# Development Tools

> Auto-generated documentation of available MCP servers, tools, skills, and agents.
> Last updated: [DATE]

## MCP Servers & Tools

### [server-name]
[Server description if available]

| Tool | Description |
|------|-------------|
| `tool-name` | Brief description |

## Agent Skills

### [skill-name]
**Description**: [description]
**Triggers**: [when to use]

## Available Agents

| Agent Type | Purpose |
|------------|---------|
| generalPurpose | Research, search, multi-step tasks |
| explore | Fast codebase exploration |
| shell | Command execution, git operations |

## Quick Reference

| Category | Count |
|----------|-------|
| MCP Servers | X |
| MCP Tools | X |
| Skills | X |
```

### Step 5: Verify Output

- Confirm file is created at `.cursor/DEVELOPMENTTOOLS.md`
- Verify all sections are populated
- Check formatting is correct

## Example Output

See [example-output.md](example-output.md) for a complete example.

## Notes

- Keep tool descriptions concise (one line each)
- Group related tools under their server
- Include tool counts in the quick reference
- Always include the "Last updated" timestamp

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dezverev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
