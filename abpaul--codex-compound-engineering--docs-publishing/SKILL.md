---
name: docs-publishing
description: Produce and publish project-facing documentation artifacts (changelogs, walkthroughs, release docs). Use when this capability is needed.
metadata:
  author: abpaul
---

# Docs Publishing

Use this skill when work includes release communication, external-facing walkthroughs, or documentation refresh after implementation.

## Coverage

- changelog generation
- release documentation sync
- feature walkthrough/video documentation
- editorial quality pass for external-facing copy

## Required Inputs

- change scope (PRs/commits/sprint tasks)
- intended audience (internal team, customers, OSS users)
- publication surface (`README`, release notes, docs site, walkthrough)
- constraints (legal, security, rollout sequencing)

## Workflow

1. Gather source-of-truth deltas from git diff, sprint docs, and merged findings.
2. Choose artifact set: changelog, rollout notes, migration notes, walkthrough updates.
3. Draft concise content with explicit "what changed", "why", and "operator actions".
4. For Rails projects, include touchpoints when relevant:
   - migrations/backfills and rollback notes
   - background job changes and idempotence expectations
   - Turbo/Hotwire behavior changes and fallback behavior
5. Validate every command/path against the repo before finalizing.
6. Run editorial pass: accuracy, consistency, and no speculative claims.
7. Publish/update docs and summarize any follow-up docs debt.

## Context Discipline

- Start with changed files and active sprint context; do not preload unrelated guides.
- Pull extra references only for unresolved questions or high-risk release notes.

## Output Contract

1. Artifact list with updated paths.
2. Summary of key user/operator impact.
3. Explicit follow-ups for deferred documentation work.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/abpaul) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
