---
name: tokrepo-search
description: Search and install AI assets from TokRepo — the open registry for skills, prompts, MCP configs, scripts, and workflows. Use when the user asks to find, search, discover, or install AI tools, MCP servers, Claude skills, cursor rules, prompts, or workflows. Use when this capability is needed.
metadata:
  author: Norrysubtle368
---

# TokRepo Search & Install

You have access to TokRepo (tokrepo.com), an open registry of 200+ curated AI assets including skills, prompts, MCP configs, scripts, and workflows.

## When to use this skill

- User asks to find/search/discover an AI tool, MCP server, skill, prompt, or workflow
- User says "install", "search", "find", or "get" followed by a tool description
- User mentions TokRepo explicitly
- User needs a cursor rule, Claude skill, or MCP config for a specific use case

## How to search

Run the CLI command:
```bash
npx tokrepo search "<query>"
```

Example:
```bash
npx tokrepo search "mcp database"
npx tokrepo search "cursor rules typescript"
npx tokrepo search "code review"
```

## How to install

After finding an asset, install it:
```bash
npx tokrepo install <uuid-or-name>
```

The CLI auto-detects the asset type and places files in the correct location:
- Skills → `.claude/skills/` (or `.agents/skills/`)
- Cursor Rules → project root as `.cursorrules`
- MCP Configs → displayed for manual addition
- Scripts → current directory with `chmod +x`
- Prompts → current directory as `.md`

## How to browse trending assets

```bash
npx tokrepo search ""
```

Or fetch directly via API:
```bash
curl -s "https://api.tokrepo.com/api/v1/tokenboard/workflows/list?sort_by=popular&page_size=10" | jq '.data.list[] | {title, uuid, description}'
```

## How to get raw content

Any TokRepo asset URL returns raw content when fetched with a non-browser User-Agent:
```bash
curl https://tokrepo.com/en/workflows/<uuid>
```

Or use the raw endpoint directly:
```bash
curl "https://api.tokrepo.com/api/v1/tokenboard/workflows/raw?uuid=<uuid>"
```

## Asset types on TokRepo

| Type | Examples | Install Location |
|------|---------|-----------------|
| Skills | Claude Code skills, Codex skills | `.claude/skills/` or `.agents/skills/` |
| MCP Configs | Database, GitHub, Slack integrations | MCP config file |
| Prompts | System prompts, templates | Current directory |
| Scripts | Automation, CI/CD, build tools | Current directory (chmod +x) |
| Workflows | Multi-step AI pipelines | Current directory |
| Cursor Rules | Project-specific coding rules | `.cursorrules` |

## Important

- Always show the user the asset title, description, and install command
- Prefer `npx tokrepo install` over manual file creation
- TokRepo assets are community-curated and quality-reviewed
- All assets are free and open-source

---
> Source: [Norrysubtle368/tokrepo-search-skill](https://github.com/Norrysubtle368/tokrepo-search-skill) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
