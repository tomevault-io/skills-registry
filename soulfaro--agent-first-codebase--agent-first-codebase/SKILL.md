---
name: agent-first-codebase
description: Diagnose and transform a codebase so coding agents (Claude Code, Codex, and similar) can work in it reliably and autonomously. Based on the practices OpenAI's Codex team used to ship a 1M-line internal product with zero human-written code. Use this skill whenever the user wants to set up or restructure AGENTS.md / CLAUDE.md, organize a docs/ directory for AI agents, diagnose why Claude Code keeps making mistakes in their repo, make their codebase "AI-friendly" or "agent-first", references "harness engineering" or Codex best practices, audits an existing repo's agent-readability, or is starting a new repo they plan to develop heavily with AI assistance. Also trigger if the user complains that their agent "forgets context", "reinvents patterns", or "ignores conventions" — those are symptoms this skill addresses at the structural level. Use when this capability is needed.
metadata:
  author: SoulFaro
---

# Agent-First Codebase

Transform a codebase into one where coding agents can reason and work reliably. Based on the practices OpenAI's Codex team used to ship a 1M-line internal product with zero human-written code.

## Core premise

Agents can only reason about what's in their runtime context. Knowledge in Slack, Google Docs, issue trackers, or teammates' heads does not exist to them. A 2,000-line AGENTS.md full of rules doesn't help either — it crowds out the actual task.

The goal of this skill is to push the project's real state into the repo as **structured, navigable, machine-checkable artifacts**, so agents can act without a human in the loop.

Five patterns underpin everything here. Understand them before proposing changes; they are the "why" behind every recommendation.

1. **Map, not encyclopedia** — A short AGENTS.md (~100 lines) that points to structured docs/, not a giant dump of rules.
2. **Progressive disclosure** — Small stable entry point; agent navigates deeper when needed.
3. **Enforced invariants** — Linters and structure tests block drift. Docs that say "please do X" drift; code that says "cannot do X" does not.
4. **Agent-readable signals** — Logs, metrics, UI state, and error messages must be queryable from within the agent's runtime. Errors should embed the fix.
5. **Golden principles + garbage collection** — A background loop (often a periodic agent task) that detects drift and files cleanup PRs.

If the user pushes back on a recommendation, open `references/philosophy.md` — it has the deeper rationale.

## Workflow

Phases go in order. **Get user confirmation between phase 2 (diagnosis) and phase 3 (execution).** Do not scaffold files until the user agrees on what's missing — scaffolding the wrong thing is worse than scaffolding nothing.

### Phase 1: Orient

Understand the starting state. The user is usually in one of three situations:

- **Greenfield** — new or near-empty repo, wants the right structure from day one.
- **Existing repo, no agent scaffolding** — has code, no AGENTS.md or CLAUDE.md, wants to add.
- **Existing repo, some scaffolding** — already has AGENTS.md/CLAUDE.md but agents still struggle; wants an audit.

If unclear, ask. Also ask (briefly — one message):

- Language/stack and monorepo layout?
- Solo project, team project, or agent-heavy?
- Have they had specific pain points with Claude Code / Codex in this repo?

The last one is gold — recurring agent failure modes are the best signal of what scaffolding is missing.

### Phase 2: Diagnose

Run through `references/diagnostic-checklist.md`. At a minimum:

1. **Top-level files** — Read AGENTS.md, CLAUDE.md, README.md if present. Count lines. Flag AGENTS.md/CLAUDE.md if >200 lines (likely encyclopedia-style).
2. **docs/ structure** — Check for subdirs: `design-docs/`, `exec-plans/`, `product-specs/`, `references/`. Flag missing ones.
3. **Architecture doc** — ARCHITECTURE.md or equivalent. Flag if missing or clearly out of sync with code.
4. **Enforcement** — Grep for custom lints, structure tests, CI jobs that run them. Agents honor what's enforced, ignore what's merely documented.
5. **Error legibility** — Skim a few lint/test error messages. Do they tell the agent how to fix the problem, or just what's wrong?
6. **Recent agent failures** — If user mentioned pain points, or if there are agent-authored PRs to browse, look for recurring failure modes. Those are the real gaps.

Produce a report with this shape:

```
# Agent-Readability Diagnosis

## Current state
- AGENTS.md / CLAUDE.md: <present? line count? map-style or encyclopedia?>
- docs/ structure: <which subdirs present/missing>
- Architecture doc: <present/missing/stale>
- Enforcement: <linters, structure tests, CI checks detected>
- Error legibility: <do errors embed fix guidance?>

## Gaps (prioritized)
1. [HIGH] <gap> — <concrete symptom this causes for agents>
2. [MED]  <gap> — <why>
3. [LOW]  <gap> — <why>

## Proposed changes
- Create <file>: <one-line purpose>
- Restructure <existing>: <what changes, why>
- Add <lint/CI check>: <what it enforces>
- Defer: <things not worth doing now, e.g. full layered architecture on a 500-line repo>
```

**Prioritize ruthlessly.** A solo side project doesn't need the full apparatus. A 10-person team building production software benefits from all of it. Match scope to the actual situation — call out explicitly when you're deferring something because it's overkill for their scale.

Share the report. Wait for user confirmation before writing files.

### Phase 3: Execute

Only after the user approves. Use templates in `assets/`:

- `assets/AGENTS.md.template` — root-level map file (~100 lines, pointers only)
- `assets/ARCHITECTURE.md.template` — top-level domain/layer boundaries
- `assets/exec-plan.md.template` — workstream plan with decision log
- `assets/docs-layout.md` — reference for docs/ subdirectory layout

When filling templates, **leave placeholders for project specifics you don't know**. Don't hallucinate team names, service boundaries, or product decisions. Write `<TODO: fill in>` with a short hint about what belongs there.

For directory scaffolding, create the shells:
- `docs/design-docs/index.md` (one line: "Index of design documents")
- `docs/exec-plans/active/` and `docs/exec-plans/completed/` (empty, with README explaining the split)
- `docs/product-specs/index.md`
- `docs/references/` (for vendored LLM-friendly reference docs like `nixpacks-llms.txt`)

Do **not** generate fake design docs, product specs, or exec plans — those capture real decisions and must come from real work. Scaffold the slots, not the contents.

For layered architecture and lint-as-guidance: consult `references/layered-architecture.md`. Recommend selectively. On a small repo, suggest it as a future direction rather than imposing it now.

For golden principles + doc-gardening loop: see `references/golden-principles.md`. Suggest 3–5 starter principles tailored to what you saw in the diagnostic, plus a doc-gardening prompt the user can run periodically (manually, via cron, or via a scheduled agent).

After executing, summarize what was created, what was left as TODOs for the user, and what was deferred.

## Things to avoid

- **Don't over-scaffold.** If the user's AGENTS.md is already map-style and under 150 lines, leave it alone. Praise what's working.
- **Don't impose a stack.** This pattern is language- and framework-agnostic. Templates use neutral placeholders.
- **Don't replace what exists blindly.** If CLAUDE.md and AGENTS.md both exist, check the contents before merging — some setups route different agents to different files. Ask.
- **Don't write code for the user.** This skill scaffolds structure. Implementing actual linters, CI jobs, and domain code is the user's (or a future agent run's) job — but do provide concrete example snippets they can adapt.
- **Don't pad the AGENTS.md.** The biggest trap is treating it as a list of every rule you can think of. Every line costs context. Write only what an agent would need on a cold start.

## Reference files

Read when relevant — keep them out of context otherwise.

- `references/philosophy.md` — The "why" behind each pattern. Read if the user pushes back or asks for rationale.
- `references/diagnostic-checklist.md` — Full diagnostic heuristics with scoring criteria.
- `references/layered-architecture.md` — The strict domain-layered architecture pattern and how to enforce it.
- `references/golden-principles.md` — Durable rules, error-as-fix-instruction, and the doc-gardening loop.

## Assets

- `assets/AGENTS.md.template` — root map file
- `assets/ARCHITECTURE.md.template` — architecture/layering doc
- `assets/exec-plan.md.template` — workstream plan template
- `assets/docs-layout.md` — docs/ subdirectory layout reference

---
> Source: [SoulFaro/agent-first-codebase](https://github.com/SoulFaro/agent-first-codebase) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
