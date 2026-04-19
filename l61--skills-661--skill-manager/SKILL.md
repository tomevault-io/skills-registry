---
name: skill-manager
description: Analyze and clean up duplicate skills across vibe coding tools. Use when user asks to analyze skills, find duplicates, or clean up their skill collection. Use when this capability is needed.
metadata:
  author: l61
---

# Skill Manager

Analyze skill inventory, detect duplicates, and clean up safely across multiple AI coding tools.

## When to Use

Use this skill when user wants to:
- Analyze their skill collection
- Find and remove duplicate skills
- Backup skills before cleanup
- List all installed skills

## Example Workflow

### Example 1: Analyzing Skills

```
user: "Analyze my skills"
→ Detect installed tools (Claude Code, OpenCode, etc.)
→ Run: bash scripts/analyze-interactive.sh
→ Show skill count per tool
→ Identify duplicate categories
→ Suggest: "Keep agent-builder, remove skill-creator"
```

### Example 2: Cleaning Up Duplicates

```
user: "Clean up my duplicate skills"
→ Review references/categories.md
→ Show duplicate table:
  Category: Agent Creation
  - Keep: agent-builder
  - Remove: agent-identifier, skill-creator, skill-writer
→ Confirm: "Remove these 3 skills?"
→ Run backup first
→ Execute: npx skills remove -g agent-identifier skill-creator skill-writer
```

### Example 3: Listing Skills

```
user: "List all my skills"
→ Detect tool: "Found Claude Code at ~/.claude/skills/"
→ List skills by category:
  Agent: agent-builder, planner
  Debug: debugging, test-driven-development
  Web: react-best-practices, web-frameworks
```

## Commands

### Analyze

```bash
bash scripts/analyze-interactive.sh
```

Shows:
- Total skill count per tool
- Duplicate categories
- Removal recommendations

### List

```bash
bash scripts/skill-manager-cli list
```

### Backup

```bash
bash scripts/skill-manager-cli backup
```

Saves to: `~/.skill-manager/backups/YYYYMMDD_HHMMSS/`

## Duplicate Categories

Check these groups (see [references/categories.md](references/categories.md)):

**Agent Creation** - Keep `agent-builder`, remove others
**Planning** - Keep `planner` + `subagent-driven-development`
**Debugging** - Keep `debugging`
**MCP** - Keep `mcp-builder` + `mcp-management`
**Frontend** - Keep `react-best-practices` + `web-frameworks`

## Safety Checklist

Before removing:

- [ ] Run backup: `bash scripts/skill-manager-cli backup`
- [ ] Review [references/categories.md](references/categories.md)
- [ ] Verify: `npx skills remove -g [skill-name]`
- [ ] Test after each batch

## Detected Tools

Auto-detects:
- `~/.claude/skills/` (Claude Code)
- `~/.config/opencode/skills/` (OpenCode)
- `~/.cursor/skills/` (Cursor)
- `~/.copilot/skills/` (GitHub Copilot)

## Output

Generates:
- Skill inventory table
- Category breakdown
- Duplicate comparison
- Safe removal commands

Reports: `~/.skill-manager/reports/`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/l61) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
