---
name: jira-reader
description: Use this skill when issue tracker information is needed - e.g., ticket details, issue search, sprint status, board overviews. Delegates to isolated worker with dedicated issue tracker MCP.
metadata:
  author: mhenze-exaring
---

# Issue Tracker Reader Skill

This skill delegates issue tracker requests to an isolated worker with dedicated MCP access.

> [!important] Worker, not Subagent
> This skill uses a **Worker** (isolated `claude -p` process), not the Task Tool.
> Use **Bash** to execute `run-worker.sh`, NOT the Task Tool.

## When to Use This Skill

- User asks about tickets or issues
- User wants sprint status or board overview
- User needs details on a specific ticket (e.g., PROJECT-1234)
- User searches for issues with specific criteria
- User asks about open team tasks

## Context

- **Team Project:** Configure in CLAUDE.md
- **Board:** Configure in CLAUDE.md

## Workflow

### Step 1: Formulate Query

Translate the user request into a concrete query:

| User Request | Query for Worker |
|--------------|------------------|
| "What's the status of PROJECT-1234?" | "Get details for PROJECT-1234" |
| "Show me open team tickets" | "Find open issues in PROJECT" |
| "What's in the current sprint?" | "Show issues in open sprints" |
| "Which bugs are open?" | "Find open bugs in PROJECT" |

### Step 2: Invoke Worker

Start the isolated Reader Worker via **Bash** (NOT Task Tool!):

```bash
.claude/workers/.shared/run-worker.sh jira-reader "<query>"
```

**Important:** Use `run_in_background: true` and monitor with `TaskOutput`.

### Step 3: Present Result

The worker outputs structured Markdown to stdout. Summarize the result for the user:

- For single tickets: Show key, status, assignee, summary
- For lists: Show overview with links and statistics
- For errors: Explain what was not found

## Example Queries

```bash
# Ticket details
.claude/workers/.shared/run-worker.sh jira-reader "Get details for PROJECT-1234"

# Open team issues
.claude/workers/.shared/run-worker.sh jira-reader "Find all open issues in PROJECT"

# Sprint backlog
.claude/workers/.shared/run-worker.sh jira-reader "List issues in open sprints for PROJECT"

# Issues by status
.claude/workers/.shared/run-worker.sh jira-reader "Find PROJECT issues in status 'In Progress'"
```

## JQL Reference (for complex queries)

The worker understands JQL syntax:

```
project = PROJECT AND status != Done
summary ~ "[Team]" AND status = "In Progress"
sprint in openSprints() AND project = PROJECT
assignee = currentUser() AND status != Done
created >= -7d AND project = PROJECT
```

## Technical Details

- **Worker:** `.claude/workers/jira-reader/`
- **MCP:** Atlassian (issue tracker read-only)
- **Permissions:** No Bash, no file write
- **Output:** Direct stdout (no file detour)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mhenze-exaring) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
