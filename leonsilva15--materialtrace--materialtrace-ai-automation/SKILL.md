---
name: materialtrace-ai-automation
description: Coordinate MaterialTrace specialist subagents and maintain repository agent guidance. Use when Codex should delegate to role-specific agents, update AGENTS.md or project skills, design agent workflows, review subagent outputs, or keep automation aligned with the source of truth and GitHub Project process. Use when this capability is needed.
metadata:
  author: LeonSilva15
---

# MaterialTrace AI Automation

Use this skill to coordinate specialist agents and maintain the project’s agent operating model.

## Required Context

Read:

- `AGENTS.md`
- active ticket or user request,
- relevant MaterialTrace skills under `skills/`,
- source-of-truth sections affected by the work.

## Subagent Routing

Delegate only when the user explicitly asks for subagents or parallel agent work.

Use specialists by ownership:

- Planner: task selection, dependency/status workflow.
- Architect: boundaries, contracts, architecture risk.
- LCA domain: rules, factors, calculations, trace, export semantics.
- Backend: FastAPI, Pydantic, SQLAlchemy, migrations, tests.
- Frontend: React/Vite workflow and UX.
- Fullstack: coordinated API/UI feature delivery.
- DevOps: Docker, CI, observability, delivery.
- QA: acceptance and regression verification.
- Demo evidence: Playwright browser scenarios, screenshots, traces, payload examples, and ticket-scoped evidence artifacts.
- Resolve ticket: end-to-end Todo pickup, specialist implementation, evidence, publish, one code-review subagent, merge, Done status, and local `master` sync.
- Publish Changes: conventional commits, PR creation, one subagent code review, review resolution, PR merge, local `master` pull, ticket notes, Done status.

Keep delegated tasks concrete, bounded, and non-overlapping. Tell workers they are not alone in the codebase and must not revert unrelated edits.

## Agent Guidance Maintenance

Update `AGENTS.md` or a project skill when a repository change makes existing guidance inaccurate or incomplete, such as:

- canonical source-of-truth location changes,
- build/test commands are introduced or changed,
- package layout or ownership boundaries become durable,
- GitHub Project workflow fields change,
- evidence capture, PR publishing, review, or Project note workflow changes,
- new implementation conventions are established.

Use `skill-creator` principles for skill edits: keep guidance concise, avoid duplicating source-of-truth content, and update `agents/openai.yaml` only when trigger behavior or user-facing metadata changes.

Include guidance maintenance in the same ticket when the new convention is learned during that work. Use a dedicated guidance branch or ticket only when the update is unrelated to active implementation.

## Ticket Start Rule

Before coordinating implementation for any new ticket, make sure the agent doing the work has synced local `master` from the configured GitHub remote and is branching from that updated `master`. If the worktree is dirty, preserve or clarify the local changes before ticket work starts.

## Review

When integrating subagent output:

- verify against source-of-truth and acceptance criteria,
- inspect changed files,
- run relevant checks,
- summarize what changed and what remains risky.

---
> Source: [LeonSilva15/MaterialTrace](https://github.com/LeonSilva15/MaterialTrace) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
