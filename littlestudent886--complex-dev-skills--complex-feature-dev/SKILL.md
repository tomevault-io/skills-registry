---
name: complex-feature-dev
description: Full-cycle 7-phase feature development workflow with persistent file-based planning (task_plan.md, findings.md, progress.md) as macro memory. Use when this capability is needed.
metadata:
  author: littlestudent886
---

# Feature Development (Complex Tasks)

A codebase-agnostic workflow for building new features safely:
- 7 phases (discovery → exploration → questions → architecture → implement → review → summary)
- Persistent planning files for long tasks (macro memory: goal, status, next actions, and durable decisions)

## Inputs

Minimum input:
- A 1–2 sentence feature description (what to build + who/why).

Helpful extras (optional):
- Acceptance criteria / examples
- Related files or modules
- Docs / tickets / prototypes (if inaccessible, ask user to paste key parts)

## Non-Negotiable Rules

1. **Never skip Phase 3 (Clarifying Questions).** If anything is underspecified, ask and **wait**.
2. **Never start Phase 5 (Implementation) without explicit approval.**
3. **Use the planning files as persistent macro memory.** Keep them concise and durable.
4. **Read before decide.** Before major decisions, re-read `task_plan.md`.
5. **Log errors briefly + don’t repeat failures.** If an action fails, change the approach.

## Quick Start (Planning Files)

This workflow requires these files in the **repo root**:
- `task_plan.md`
- `findings.md`
- `progress.md`
- `CLAUDE.md` (agent instructions; created by init if missing)

To initialize them, run:
- `/complex-feature-dev:init` (recommended)

Or via terminal:
- macOS/Linux (or Windows Git Bash): `bash "$(ls -dt ~/.claude/plugins/cache/complex-dev-skills/complex-feature-dev/*/scripts/init-session.sh 2>/dev/null | head -1)"`
- Windows PowerShell: `pwsh -ExecutionPolicy Bypass -File "~/.claude/plugins/cache/complex-dev-skills/complex-feature-dev/<version>/scripts/init-session.ps1"`

## Project Guidelines

- Follow `AGENTS.md` / `CLAUDE.md` / `CONTRIBUTING.md` / `README.md` if present.
- Prefer matching existing code patterns over introducing new abstractions.
- Write important conventions you discover into `findings.md`.

---

## Phase 1: Discovery

**Goal:** Make the request concrete and testable.

- If planning files are missing, run the initializer script via the Bash tool and do not proceed until the files exist.
- Restate the request as acceptance criteria; confirm scope and non-goals.
- Capture constraints (compatibility, performance, time, rollout).
- Write brief confirmed requirements + acceptance criteria to `findings.md`.

## Phase 2: Codebase Exploration

**Goal:** Identify the correct integration points and existing patterns.

Do 2–3 independent exploration passes:
- Pass A: find similar features and trace end-to-end
- Pass B: map architecture boundaries and extension points
- Pass C: identify testing/build/lint/config/observability conventions

For each pass:
- record key files/patterns (prefer file:line; keep it macro) in `findings.md`.

Also:
- locate and follow `AGENTS.md` instructions (if present).

## Phase 3: Clarifying Questions (Hard Stop)

**Goal:** Resolve all ambiguity before architecture and implementation.

Ask questions grouped by:
- behavior definition (inputs/outputs/state)
- edge cases (empty values, duplicates, concurrency, ordering, permissions)
- error handling (timeouts, retries, idempotency, rollback)
- integrations (APIs, data models, config, feature flags)
- backward compatibility & migration
- performance assumptions
- observability
- testing & acceptance plan

Wait for answers (or propose defaults and get explicit confirmation). Record final answers in `findings.md`.

## Phase 4: Architecture Design

**Goal:** Present 2–3 viable approaches and let the user choose.

Provide at least:
- Minimal changes (reuse, low risk)
- Pragmatic balance (cleaner boundaries)
- Optional clean architecture (more refactor, higher long-term clarity)

For each approach include:
- files to create/modify
- key abstractions + data flow
- trade-offs (risk/time/testability/maintainability)

Write the chosen approach + rationale (brief) to `findings.md` and `task_plan.md`.

## Phase 5: Implementation (Requires Approval)

**Goal:** Implement the chosen approach.

- Implement in small verifiable increments.
- Follow existing conventions; avoid unrelated refactors.
- Log key actions/files/tests in `progress.md` (brief).
- Log errors in `task_plan.md` (and brief resolution in `progress.md`).

## Phase 6: Quality Review

**Goal:** Catch high-impact issues before delivery.

Default review scope:
- Prefer reviewing `git diff` (or specified files).

Focus areas:
- correctness, security/permissions, error handling, observability, conventions, simplicity

Confidence filtering:
- score issues 0–100
- report only issues ≥ 80 confidence, with concrete fixes (file:line)

## Phase 7: Summary

**Goal:** Deliver a clean handoff.

- Summarize what was built, key decisions, and files modified.
- Provide verification steps (commands/endpoints/UI paths).
- Call out risks and suggested next steps.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/littlestudent886) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
