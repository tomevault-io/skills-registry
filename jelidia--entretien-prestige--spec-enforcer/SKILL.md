---
name: spec-enforcer
description: Verify code matches project requirements in docs/tasks/DOCS_TASK_LIST.md. Use before completing features or deploying. Use when this capability is needed.
metadata:
  author: jelidia
---

# spec-enforcer Skill
## Verify code matches project requirements

## When to use
Before completing a feature, before deploying

## Example
`/spec-enforcer Verify that photo upload matches spec`

## What it does
1. Reads docs/tasks/DOCS_TASK_LIST.md for requirements
2. Checks your implementation
3. Lists what matches, what's missing, what's wrong
4. Suggests fixes if needed

## Quality checks
- All required fields present
- Validation matches spec
- French labels correct
- Mobile responsive
- Tests present where required by spec or changes

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jelidia) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
