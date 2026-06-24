---
name: claude-dev-team
description: Multi-agent development workflow using Agent Teams. Supports four modes: plan (architect + PM debate → plan.md), dev (developer + tester + reviewer iterate → code), full (plan → approval gate → dev), and auto (plan → dev, no gate). Use when tasks benefit from collaborative agent roles with peer messaging. Use when this capability is needed.
metadata:
  author: rube-de
---

# Claude Dev Team

Multi-agent development workflow with four modes. Pick one based on the user's needs:

| Mode | When to use |
|------|-------------|
| **plan** | Need architecture/design before coding |
| **dev** | Have an approved plan, ready to implement |
| **full** | End-to-end with user approval gate between plan and dev |
| **auto** | End-to-end without approval gate |

Before executing any mode, read [references/WORKFLOW.md](references/WORKFLOW.md) for detailed step-by-step instructions, spawn prompts, and output templates.

## Architecture

```
Plan Phase (plan/full/auto)     Dev Phase (dev/full/auto)
  Lead (You)                      Lead (You)
  ├── architect  [teammate]       ├── developer  [teammate]
  ├── prod-mgr   [teammate]      ├── tester     [teammate]
  └── researcher [subagent]       ├── reviewer   [teammate]
                                  └── researcher [subagent]
         │                                │
         └──── plan.md (handoff) ─────────┘
```

**Teammates** message each other directly (Architect↔PM, Dev↔Tester↔Reviewer).
**Researcher** is a subagent — Lead relays results.

## Roles

### Researcher (subagent — spawn via Task without team_name)

Research specialist for doc lookups. Queries Context7 for library docs, searches web for best practices, returns structured findings with code examples. Bundled as `agents/researcher.md` in this plugin — Context7 MCP is auto-configured via `.mcp.json`.

### Architect (teammate — plan phase)

Designs architecture: components, interfaces, file changes, data flow, testing strategy. Debates tradeoffs with PM. Messages design to lead and PM.

### Product Manager (teammate — plan phase)

Validates architecture against requirements. Challenges design with concerns. Produces verdict: APPROVED or NEEDS_REVISION with specifics.

### Developer (teammate — dev phase)

Implements tasks from plan. No stubs, no TODOs. Matches existing patterns. Iterates with tester on failures, reviewer on issues.

### Tester (teammate — dev phase)

Writes and runs tests matching existing patterns. Messages developer with specific failures + root cause. Max 3 fix cycles, then escalates to lead.

### Reviewer (teammate — dev phase)

Reviews changed files for completeness, correctness, security, quality, plan adherence. Validates review with `/council` (`quick quality` for routine, `review security` or `review architecture` for critical concerns). Scans for stubs. Messages developer with file:line + fix suggestions. Max 3 cycles.

## Rules

- One team at a time — cleanup plan-team before starting dev-team
- Teammates debate directly (Architect↔PM, Dev↔Tester, Dev↔Reviewer)
- Researcher is always a subagent — Lead relays results
- Plan.md is the single source of truth and handoff artifact
- Every task declares `depends_on`; parallel within waves, sequential between
- Verify build between waves
- Avoid file conflicts between parallel tasks
- Testing + review are mandatory quality gates
- Always cleanup team before finishing
- Plan mode: do NOT implement — only plan
- If stuck — ask user, don't loop

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rube-de) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
