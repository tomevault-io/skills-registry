---
name: research
description: Use this skill for research across multiple sources (issue trackers, wikis, chat). Activates on "find information about X in multiple sources", "search all sources for Y", "what was communicated about Z".
metadata:
  author: mhenze-exaring
---

# Research Skill

This skill delegates multi-source research requests to an isolated worker with access to configured external sources and the local Obsidian vault.

> [!important] Worker, not Subagent
> This skill uses a **Worker** (isolated `claude -p` process), not the Task Tool.
> Use **Bash** to execute `run-worker.sh`, NOT the Task Tool.

## When to Use This Skill

Activate when the user wants information from **multiple sources**:

- "Find all information about X in Jira and Confluence"
- "Search for Y in all sources"
- "What has been communicated about Z?"
- "Research topic X across all available sources"
- "Gather context about feature Y from all available sources"

**Single-source requests** → Use specialized tools/workers instead:
- Issue tracker only → `jira-reader` skill
- Vault only → `mcp__obsidian__search_vault_smart`

## Available Sources

| Source | Type | Access |
|--------|------|--------|
| **Obsidian Vault** | Local knowledge base | `mcp__obsidian__*` |
| **Issue Tracker** | Issues, epics, comments | Configured MCP |
| **Documentation** | Wiki pages | Configured MCP |
| **Chat** | Conversations, threads | Configured MCP |

## Workflow

### Step 1: Analyze Request

Identify:
1. **Topic/Keywords** - What to search for
2. **Sources** - Which sources to include (default: all)
3. **Time scope** - If relevant (e.g., "last 2 weeks")
4. **Output format** - Summary, detailed report, or link collection

### Step 2: Formulate Query

Translate the user request into a research task:

| User Request | Query for Worker |
|--------------|------------------|
| "Find info about feature X in Jira and Confluence" | "Research feature X: search issue tracker for related issues and documentation" |
| "What did team communicate about topic Y in Slack?" | "Search chat for topic Y discussions in the last 2 weeks" |
| "Gather all context about feature Z" | "Research feature Z across all sources: Vault, issues, documentation, chat" |

### Step 3: Invoke Worker

Start the isolated Research Worker via **Bash** (NOT Task Tool!):

```bash
.claude/workers/.shared/run-worker.sh research "<research-task>"
```

**Important:** Use `run_in_background: true` and monitor with `TaskOutput`.

### Step 4: Present Result

The worker outputs a structured research report. Present to the user:

- **Summary** of findings
- **Key sources** with links
- **Open questions** if information was incomplete

## Example Queries

```bash
# Multi-source research
.claude/workers/.shared/run-worker.sh research "Research feature X implementation: search issues for related tickets, documentation for technical docs, and chat for recent discussions"

# Issue tracker + Documentation
.claude/workers/.shared/run-worker.sh research "Find all information about pricing feature in issues and documentation"

# All sources
.claude/workers/.shared/run-worker.sh research "Gather comprehensive context about login feature from Vault, issues, documentation, and chat"

# Time-scoped
.claude/workers/.shared/run-worker.sh research "Search chat for discussions about topic Y since last month"
```

## Research Hierarchy

The worker follows this priority:

1. **Obsidian Vault** - Check local knowledge first
2. **Issue Tracker** - Issues, epics, comments for project context
3. **Documentation** - Specs and documentation
4. **Chat** - Recent discussions and decisions

## Output Format

The worker returns a structured markdown report:

```markdown
# Research: [Topic]

**Date:** YYYY-MM-DD
**Status:** Complete | Partial | Insufficient

## Summary
[2-3 sentence executive summary]

## Key Findings
- Finding 1
- Finding 2

## Sources

### Issues
- [PROJECT-XXX](url) - Description

### Documentation
- [Page Title](url) - Description

### Chat
- [Thread](url) - Key discussion point

## Open Questions
- Unanswered question 1
```

## Technical Details

- **Worker:** `.claude/workers/research/`
- **MCP:** Configured sources (Obsidian, Atlassian, Slack, etc.)
- **Permissions:** Read-only for all sources
- **Output:** Direct stdout (structured markdown)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mhenze-exaring) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
