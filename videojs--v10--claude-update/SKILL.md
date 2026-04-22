---
name: claude-update
description: >- Use when this capability is needed.
metadata:
  author: videojs
---

# Claude Update

Review any feedback, preferences, suggestions or rules that the user provided during task and 
update AI documentation to incorporate them as appropriate.

## When to Update

- New naming conventions or code patterns
- New anti-patterns discovered during implementation
- Changes to component architecture, accessibility, or API design
- New utilities or shared patterns

## Where to Put It

| Type                          | Location             |
| ----------------------------- | -------------------- |
| Cross-cutting (naming, utils) | CLAUDE.md Code Rules |
| Component patterns            | `component` skill    |
| Accessibility                 | `aria` skill         |
| API design / DX               | `api` skill          |
| Documentation                 | `docs` skill   |

**Decision:** Ask "Who needs this?" — if domain-specific, use a skill.

## After Modifying Skills

Check consistency:

1. `.claude/skills/README.md` — Quick Reference, Skills table
2. `CLAUDE.md` — if skill changes affect repo-wide conventions

## Process

1. Identify what changed (pattern, convention, anti-pattern)
2. Route to correct location (table above)
3. Make the update
4. Run consistency checks (if modifying skills)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/videojs) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
