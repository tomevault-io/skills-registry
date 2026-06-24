---
name: audit-agent-docs
description: Audit Codex AGENTS.md and AGENTS.override.md instruction files in the current project for staleness and coverage gaps. Scores freshness with git churn, flags content drift, discovers directories that may deserve scoped AGENTS.md guidance, and reports ranked findings with per-file approval before edits. Global and project-agnostic. Trigger when the user says "audit AGENTS.md", "audit agent docs", "audit-agent-docs", "are my AGENTS.md files up to date", "check if AGENTS.md is stale", "Codex docs health check", or "refresh agent docs". SKIP when the user only wants to create a new AGENTS.md from scratch or only wants placement recommendations (use where-agents-md instead). Use when this capability is needed.
metadata:
  author: ada-ggf25
---

# Audit AGENTS.md files

Audit Codex project instruction files for staleness and coverage gaps. Treat
`AGENTS.md` and `AGENTS.override.md` as the Codex instruction surface.

## Staleness Model

### Scope Boundary

Each `AGENTS.md` or `AGENTS.override.md` owns its directory and all subdirectories that
do not have their own agent instruction file. Use this scoped ownership to attribute git
churn: a commit that only touches `frontend/` should not make the root file stale if
`frontend/AGENTS.md` exists.

### Git Churn Signal

For each instruction file, use its last-commit timestamp:

```bash
git log -1 --format=%aI -- <path>
```

Count commits in scope since then, excluding noise:

| Commits in scope since last touch | Signal |
|---|---|
| 0-4 | Likely fresh |
| 5-14 | Possibly stale |
| 15+ | Likely stale |

Also treat the file as likely stale if manifest files, CI config, or top-level config
changed since it was last touched.

### Noise Filter

Exclude lock files, generated output, dependency directories, virtualenvs, caches,
coverage, vendored code, binary assets, and anything ignored by git. Count source,
manifest, config, test, and documentation changes.

## Procedure

### 1. Orient

- Confirm the cwd is the intended repo root when ambiguous.
- Find existing files:
  `find . \( -name AGENTS.md -o -name AGENTS.override.md \) -not -path '*/node_modules/*' -not -path '*/.git/*'`
- If none exist, say so and suggest creating a root `AGENTS.md`, then stop.
- For a large repo, delegate broad directory exploration to a read-only subagent when
  available and work from its summary.

### 2. Build The Scope Map

- For each instruction file, determine its owned scope: its directory minus any child
  subtree with its own `AGENTS.md` or `AGENTS.override.md`.
- Track parent-child relationships so churn is not double-counted.

### 3. Score Staleness

For each file:

- Get the last-touch timestamp with `git log`.
- Count scoped commits since that timestamp.
- Check whether manifests, CI, build tooling, or top-level config changed in scope.
- Assign: **Likely fresh**, **Possibly stale**, **Likely stale**, or **Orphaned**.

### 4. Content Drift Check

For possibly stale and likely stale files, read the content and flag:

- paths, commands, or tools that no longer exist;
- conventions contradicted by the live codebase;
- missing major modules, frameworks, workflows, or safety constraints;
- instructions that are too product-specific for the actual repo usage.

Keep notes short and evidence-based.

### 5. Coverage Gap Scan

Apply the `where-agents-md` heuristic to find directories that clearly deserve scoped
Codex guidance but lack it. Report only strong candidates.

### 6. Report

Present findings in this order:

1. **Likely stale** - recommend targeted update.
2. **Orphaned** - recommend deletion or consolidation.
3. **Missing** - recommend adding scoped `AGENTS.md`.
4. **Possibly stale** - recommend manual review.
5. **Likely fresh** - list briefly with last-touch date.

For each finding include path, last-touch date, churn count, and a one-line rationale.

### 7. Act Only With Approval

Offer targeted edits per file. Get explicit per-file approval before modifying or
deleting any instruction file. Never batch-rewrite agent docs silently.

## Guardrails

- Never modify or delete `AGENTS.md` or `AGENTS.override.md` without explicit per-file
  approval.
- Attribute churn by scope; do not charge parent docs for child-scoped work.
- Apply the noise filter consistently.
- Bias coverage recommendations toward restraint.
- Never fabricate repo facts; base findings on files and git history observed locally.

---
> Source: [ada-ggf25/AI-Tools](https://github.com/ada-ggf25/AI-Tools) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
