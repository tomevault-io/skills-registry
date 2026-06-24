---
name: ralph-it
description: Set up, manage, and run ralph-it autonomous agent loops on projects. Use when: (1) Setting up ralph-it on a new or existing project, (2) Creating GitHub issues for autonomous processing, (3) Writing or editing RALPH.md prompt templates, (4) Running, monitoring, or debugging ralph-it loops, (5) Configuring agents (Claude, Codex, Gemini, Aider), (6) Working with ralph-it labels, logs, or lock files. Covers: installation, issue format standards, RALPH.md syntax, multi-LLM configuration, label system, logging, and GitHub Actions CI/CD. Use when this capability is needed.
metadata:
  author: mikkelkrogsholm
---

# ralph-it Skill

Autonomous agent loop runner powered by GitHub Issues. Zero dependencies, built with Bun.

See `references/` for detailed documentation on each topic.

## Quick Start

```bash
# In your project directory:
ralph-it init              # Create RALPH.md + .ralph/
ralph-it setup             # Create 19 labels on GitHub
ralph-it doctor            # Verify everything works
ralph-it run --watch       # Start autonomous loop
```

## Quick Decision Trees

### "I need to set up ralph-it on a project"

```
Setup ralph-it?
├─ Fresh project → ralph-it init, then ralph-it setup, then ralph-it doctor
├─ Existing project → Same flow, edit RALPH.md to match your project
├─ CI/CD only → See references/github-actions.md
└─ Different agent (not Claude) → See references/agents.md for config
```

After setup, create issues and label them `ralph:queued`. See [references/issues.md](references/issues.md) for the standard format.

### "I need to create issues for ralph-it"

```
Create issues?
├─ Single task → Use the standard issue template (see below)
├─ Batch from TODOs → Search codebase for TODO/FIXME, create one issue per item
├─ From backlog → Add ralph:queued label to existing issues
└─ Sub-tasks → Create separate issues, use priority labels to order them
```

**CRITICAL: Issue format matters.** Structured issues give the agent clear context. Always use the standard format — see [references/issues.md](references/issues.md).

### "I need to write or edit RALPH.md"

```
RALPH.md?
├─ New project → ralph-it init gives you a template, customize it
├─ Add commands → command.name = ./script.sh (with optional timeout)
├─ Change agent → agent.command, agent.args, agent.input
├─ Add context → Use {{ }} placeholders for repo, issue, commands, args
├─ Private notes → Use <!-- HTML comments --> (stripped from prompt)
└─ Full syntax → See references/ralph-md.md
```

### "I need to run or monitor a loop"

```
Run loop?
├─ Process all queued → ralph-it run
├─ Single issue → ralph-it run --issue 42
├─ Watch mode → ralph-it run --watch --interval 30
├─ Filter issues → ralph-it run --label type:bug --milestone v2
├─ Monitor live → tail -f .ralph/logs/*.jsonl
├─ Check status → ralph-it status
├─ See queue → ralph-it list
└─ Troubleshoot → ralph-it doctor
```

## Standard Issue Format

When creating issues for ralph-it, ALWAYS use this format:

```markdown
## Context
Brief background — what exists now, what's the problem or need.

## Task
Precise description of what the agent should do. Be specific.

## Acceptance Criteria
- [ ] Tests pass
- [ ] No new lint errors
- [ ] [Task-specific criteria]

## Scope
Files and modules the agent should focus on.
- `src/auth/` — authentication module
- `src/api/routes.ts` — API routes

## Constraints
What the agent should NOT do.
- Do not modify the database schema
- Do not change public API signatures
```

**Required labels:** Always add `ralph:queued` + one `type:` label + one `priority:` label.

**Why this format:** Each section maps to what the agent needs:
- **Context** → understands the "why"
- **Task** → knows exactly what to do
- **Acceptance Criteria** → knows when it's done
- **Scope** → stays focused, doesn't wander
- **Constraints** → avoids breaking things

## Label System Overview

| Group | Labels | Purpose |
|-------|--------|---------|
| **State** | `ralph:queued`, `ralph:in-progress`, `ralph:done`, `ralph:failed`, `ralph:blocked` | Managed by ralph-it automatically |
| **Priority** | `priority:critical`, `priority:high`, `priority:medium`, `priority:low` | Determines processing order |
| **Type** | `type:bug`, `type:feature`, `type:refactor`, `type:docs`, `type:test`, `type:chore`, `type:security`, `type:perf`, `type:migration`, `type:research` | Categorization |

Full details: [references/labels.md](references/labels.md)

## RALPH.md Frontmatter Quick Ref

```
---
agent.command = claude
agent.args = -p --dangerously-skip-permissions
agent.input = stdin
agent.timeout = 600
credit = true
command.coverage = ./check-coverage.sh
command.coverage.timeout = 120
command.lint = npm run lint
---
```

| Field | Required | Default | Description |
|-------|----------|---------|-------------|
| `agent.command` | Yes | — | Agent CLI executable |
| `agent.args` | No | — | CLI arguments |
| `agent.input` | No | `stdin` | `stdin`, `file`, or `argument` |
| `agent.timeout` | No | `600` | Agent timeout (seconds) |
| `command.<name>` | No | — | Shell command for `{{ commands.<name> }}` |
| `command.<name>.timeout` | No | `60` | Command timeout (seconds) |
| `credit` | No | `true` | Append co-author instruction |

## Template Placeholders

| Placeholder | Content |
|-------------|---------|
| `{{ repo.description }}` | Repository description |
| `{{ repo.languages }}` | Languages in the repo |
| `{{ issue.number }}` | Issue number |
| `{{ issue.title }}` | Issue title |
| `{{ issue.body }}` | Full issue body |
| `{{ issue.comments }}` | Formatted comment thread |
| `{{ issue.labels }}` | Comma-separated labels |
| `{{ commands.<name> }}` | Output from named command |
| `{{ args.<name> }}` | Runtime argument (`--arg key=value`) |
| `{{ ralph.iteration }}` | Current iteration number |

## Multi-LLM Quick Config

| Agent | Config |
|-------|--------|
| **Claude** | `agent.command = claude` / `agent.args = -p --dangerously-skip-permissions` / `agent.input = stdin` |
| **Codex** | `agent.command = codex` / `agent.args = exec --full-auto -` / `agent.input = stdin` |
| **Gemini** | `agent.command = gemini` / `agent.args = --prompt - --yolo` / `agent.input = stdin` |
| **Aider** | `agent.command = aider` / `agent.args = --message-file {prompt_file} --yes` / `agent.input = file` |

Full agent configuration guide: [references/agents.md](references/agents.md)

## Reference Index

| Topic | Reference |
|-------|-----------|
| Installation & setup | [references/setup.md](references/setup.md) |
| Issue format standard | [references/issues.md](references/issues.md) |
| RALPH.md syntax | [references/ralph-md.md](references/ralph-md.md) |
| Command reference | [references/commands.md](references/commands.md) |
| Label system | [references/labels.md](references/labels.md) |
| Agent configuration | [references/agents.md](references/agents.md) |
| Logging & monitoring | [references/logging.md](references/logging.md) |
| End-to-end workflow | [references/workflow.md](references/workflow.md) |
| GitHub Actions CI/CD | [references/github-actions.md](references/github-actions.md) |

---
> Source: [mikkelkrogsholm/ralph-it](https://github.com/mikkelkrogsholm/ralph-it) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
