---
name: sync-dev-guide
description: Keeps .cursor/rules/GeneralDevGuide.mdc in sync with available skills and MCP servers. Discovers skills from .cursor/skills/, agents from .cursor/agents/, and MCP servers from mcps/; rebuilds the Skills & Agents tables and the MCP Servers table. Use when adding or removing skills or MCP servers, when the user says "sync dev guide", "update GeneralDevGuide", "keep rules in sync with skills", or "refresh dev guide from skills and MCP". Use when this capability is needed.
metadata:
  author: dezverev
---

# Sync Dev Guide

Updates `.cursor/rules/GeneralDevGuide.mdc` so its **Skills & Agents** and **MCP Servers** sections match what is available on disk. Run after adding/removing skills, agents, or MCP servers.

## When to Apply

- User says "sync dev guide", "update GeneralDevGuide", or "keep rules in sync with skills"
- A new skill was added under `.cursor/skills/` or an agent under `.cursor/agents/`
- A new MCP server was added under `mcps/` (or the project’s MCP root)
- User asks to refresh the dev guide from the current skills and MCP servers

## Workflow

### 1. Discover skills

1. List `.cursor/skills/` (each subdir is a skill).
2. For each skill dir, read `.cursor/skills/<dir>/SKILL.md` and parse the **YAML frontmatter** (between the first `---` and the next `---`).
3. From frontmatter, take:
   - **name**: skill name (e.g. `smart-test-runner`).
   - **description**: use as the "Use when" text for the table. If it’s long, use the sentence that starts with "Use when" up to the next period, or the first sentence.

### 2. Discover agents

1. List `.cursor/agents/` and collect every `.md` file.
2. For each file, read the YAML frontmatter and take **name** and **description** (same rule as skills for "Use when").

### 3. Discover MCP servers

1. Find the MCP root: prefer `mcps/` at workspace root, or `.cursor/projects/<project>/mcps/` if that’s where descriptors live (see GeneralDevGuide “Descriptor location”).
2. List direct subdirs of that root; each subdir is one server (identifier = dir name, e.g. `user-Playwright`).
3. For each server dir, if `SERVER_METADATA.json` exists, read it and keep **serverIdentifier** and **serverName**.

### 4. Map skills to categories

Use this mapping to build the three skill tables. If a skill is not listed, put it in **Skills & agents (meta)**.

| Category | Skill names |
|----------|-------------|
| **Test & verification** | smart-test-runner, map-tests-to-tags, add-plugin-test, analyze-test-json, check-test-logical-resp |
| **Plugins & providers** | add-plugin, integrate-mcp-server, create-provider, sync-providers, review-plugin |
| **Skills & agents (meta)** | recommend-skill-design, create-project-skill, sync-dev-guide, and any other unlisted skill |

### 5. Build the new rule content

1. **Leave everything else unchanged**: preamble, "General Workflow", "Test Verification Rule", "Project Structure Reference", and all section headings/text outside the tables.
2. **Skills & Agents** (from "## Test & verification" through the last agents/meta table):
   - **Test & verification**: one markdown table, columns `| Skill | Use when |`, one row per skill in that category.
   - **Plugins & providers**: same format for that category.
   - **Skills & agents (meta)**: one table for skills in that category.
   - **Agents**: one table `| Skill / Agent | Use when |` with one row per agent (use agent **name** in the first column, **description** as "Use when").
3. **MCP Servers** (from "## Available MCP servers" through "## Descriptor location" inclusive):
   - Keep the **Rules for using MCP tools** and **Descriptor location** subsections as-is (or from current file).
   - **Available MCP servers**: one markdown table with columns `| Server (identifier) | Purpose | Use when … |`.
   - For each server discovered in step 3:
     - If that server identifier already appears in the **current** GeneralDevGuide “Available MCP servers” table, **reuse** that row’s Purpose and “Use when” text.
     - If it’s new, add a row: identifier, **Purpose** = `serverName` from SERVER_METADATA (or identifier if missing), **Use when** = `List mcps/<server>/tools/ for tools; see SERVER_METADATA.json.`

### 6. Apply edits

1. Open `.cursor/rules/GeneralDevGuide.mdc`.
2. Replace only the block that starts at `## Test & verification` (under "## Skills & Agents") and ends at the end of the last skill/agent table (do not change "## MCP Servers" or "## Rules for using MCP tools" yet).
3. Replace the block that starts at `## Available MCP servers` and ends at the end of `## Descriptor location` with the new MCP section built in step 5.

Use search_replace (or equivalent) so the rest of the file (frontmatter, headings, prose) is unchanged.

## Output format

- Skills table rows: `| **skill-name** | \<description or "Use when" sentence\> |`
- Agents table rows: `| **agent-name** (agent) | \<description or "Use when" sentence\> |`
- MCP table rows: `| **serverIdentifier** | Purpose text | Use when text |`

Use the same heading levels and table style as the existing GeneralDevGuide (e.g. `## Test & verification`, `## Plugins & providers`, `## Skills & agents (meta)` under `# Skills & Agents`).

## Paths reference

| What | Path |
|------|------|
| Rule file | `.cursor/rules/GeneralDevGuide.mdc` |
| Skills | `.cursor/skills/<name>/SKILL.md` |
| Agents | `.cursor/agents/*.md` |
| MCP root | `mcps/` at workspace root, or `.cursor/projects/<project>/mcps/` |
| Server metadata | `mcps/<server>/SERVER_METADATA.json` |

## Verification

After editing:

- Every skill in `.cursor/skills/` appears in exactly one of the three skill tables.
- Every agent in `.cursor/agents/` appears in the agents table.
- Every MCP server dir under the MCP root appears in the “Available MCP servers” table.
- No extra skills/agents/servers appear that do not exist on disk.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dezverev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
