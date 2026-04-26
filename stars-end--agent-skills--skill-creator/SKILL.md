---
name: skill-creator
description: | Use when this capability is needed.
metadata:
  author: stars-end
---

# Skill Creator (Deprecated)

This skill remains only to preserve old trigger phrases.

## Canonical Routing

- For creating or updating shared skills in `~/agent-skills`, use `agent-skills-creator`.
- For "write a comprehensive implementation plan with Beads epic, dependencies, and subtasks", use `implementation-planner`.
- If Codex cannot see a newly added canonical skill yet, repair the Codex user skills plane with `~/agent-skills/scripts/dx-codex-skills-install.sh --apply` and restart Codex.

## Why This Skill Was Deprecated

This legacy skill taught assumptions that are no longer the active repo contract:
- `.claude/skills/...` as the primary layout
- legacy V3 wording as the source of truth
- outdated helper flows that are not part of the current repo
- historical guidance that predates the current baseline regeneration contract

## What To Do Instead

### Canonical Skill Authoring

Use `agent-skills-creator` when the task is:
- add a skill
- refactor a skill
- deprecate a skill
- regenerate baseline after skill changes

### Canonical Planning

Use `implementation-planner` when the task is:
- implementation plan
- migration spec
- epic + subtasks + dependencies
- rollout plan

## Compatibility Rule

If this skill triggers because the user said "skill-creator":
1. keep the user intent
2. route the actual work to `agent-skills-creator`
3. only keep this skill name alive long enough to avoid breaking legacy activation patterns

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/stars-end) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
