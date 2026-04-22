---
name: sync-dev-guide-claude
description: Keeps CLAUDE.md in sync with available skills, agents, and MCP servers. Discovers skills from .claude/skills/, agents from .claude/agents/, and MCP servers via `claude mcp list`; rebuilds the Skills & Agents and MCP Servers sections. Use when adding or removing skills, agents, or MCP servers, when the user says "sync dev guide", "update CLAUDE.md", "keep rules in sync with skills", or "refresh dev guide". Use when this capability is needed.
metadata:
  author: dezverev
---

# Sync Dev Guide

Updates `CLAUDE.md` so its **Skills & Agents** and **MCP Servers** sections match what is available. Run after adding/removing skills, agents, or MCP servers.

## When to Apply

- User says "sync dev guide", "update CLAUDE.md", or "keep rules in sync with skills"
- A new skill was added under `.claude/skills/` or an agent under `.claude/agents/`
- A new MCP server was added via `claude mcp add`
- User asks to refresh the dev guide from the current skills and MCP servers

## Workflow

### 1. Discover skills

1. List `.claude/skills/` (each subdir is a skill).
2. For each skill dir, read `.claude/skills/<dir>/SKILL.md` and parse the **YAML frontmatter** (between the first `---` and the next `---`).
3. From frontmatter, take:
   - **name**: skill name (e.g. `smart-test-runner`).
   - **description**: use as the "Use when" text for the table. If it's long, use the sentence that starts with "Use when" up to the next period, or the first sentence.

### 2. Discover agents

1. List `.claude/agents/` and collect every `.md` file.
2. For each file, read the YAML frontmatter and take **name** and **description** (same rule as skills for "Use when").

### 3. Discover MCP servers

1. Run `powershell -Command "claude mcp list"` to get all configured MCP servers (use PowerShell as bash doesn't work for this command).
2. Parse the output to extract from each line (format: `<name>: <command> - ✓ Connected`):
   - **Server name** (the identifier before the colon)
   - **Command** (the full command between colon and dash, e.g., `cmd /c npx @playwright/mcp@latest`)
   - **Connection status** (✓ Connected)
   - **Transport** type: stdio for npx-based servers (infer from command pattern)
3. Skip the header line "Checking MCP server health...".
4. If output is "No MCP servers configured. Use `claude mcp add` to add a server." or similar, note this in the output.

### 4. Map skills to categories

Use this mapping to build the three skill tables. If a skill is not listed, put it in **Skills & agents (meta)**.

| Category | Skill names |
|----------|-------------|
| **Test & verification** | smart-test-runner, map-tests-to-tags, add-plugin-test, analyze-test-json, check-test-logical-resp |
| **Plugins & providers** | add-plugin, create-provider, sync-providers, review-plugin |
| **Skills & agents (meta)** | recommend-skill-design, create-project-skill, sync-dev-guide, and any other unlisted skill |

### 5. Build the new rule content

1. **Leave everything else unchanged**: preamble, "General Workflow", "Test Verification Rule", "Project Structure Reference", and all section headings/text outside the tables.
2. **Skills & Agents** (from "## Test & verification" through the last agents/meta table):
   - **Test & verification**: one markdown table, columns `| Skill | Use when |`, one row per skill in that category.
   - **Plugins & providers**: same format for that category.
   - **Skills & agents (meta)**: one table for skills in that category. Update the **sync-dev-guide** description to mention "or after adding/removing skills or MCP servers".
   - If agents exist, add an additional row or table section `| Skill / Agent | Use when |` with one row per agent (use agent **name** in the first column, **description** as "Use when").
3. **MCP Servers** (from "# MCP Servers" section):
   - Keep the **Discovering MCP Servers** subsection as-is (with the `claude mcp list` and `claude mcp add` commands).
   - **Available MCP Servers** subsection:
     - If no MCP servers are configured, state: "Currently no MCP servers are configured. Use `claude mcp add` to add servers."
     - If MCP servers exist, create a markdown table with columns `| Server | Transport | Description |`.
     - For each server from step 3, add a row with server name, transport type, and command/description.

### 6. Apply edits

1. Open `CLAUDE.md`.
2. Replace the block that starts at `## Test & verification` (under "# Skills & Agents") and ends at the end of the last skill/agent table.
3. Replace the block from `## Available MCP Servers` to the end of the MCP Servers section with the newly generated content.

Use search_replace (or equivalent) so the rest of the file (headings, prose) is unchanged.

## Output format

- Skills table rows: `| **skill-name** | \<description or "Use when" sentence\> |`
- Agents table rows: `| **agent-name** (agent) | \<description or "Use when" sentence\> |`
- MCP servers table rows: `| **server-name** | transport-type | command and description |` (e.g., `` `npx @playwright/mcp@latest` - Browser automation and testing``)

Use the same heading levels and table style as the existing CLAUDE.md (e.g. `## Test & verification`, `## Plugins & providers`, `## Skills & agents (meta)` under `# Skills & Agents`).

## Paths reference

| What | Path/Command |
|------|------|
| Rule file | `CLAUDE.md` |
| Skills | `.claude/skills/<name>/SKILL.md` |
| Agents | `.claude/agents/*.md` |
| MCP servers | `powershell -Command "claude mcp list"` command |

## Verification

After editing:

- Every skill in `.claude/skills/` appears in exactly one of the three skill tables.
- Every agent in `.claude/agents/` appears in the agents table (if agents exist).
- Every MCP server from `claude mcp list` appears in the MCP Servers section.
- No extra skills/agents/servers appear that do not exist.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dezverev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
