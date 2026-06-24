---
name: skill-authoring
description: Create high-quality skills: scoped, procedural, and durable. Prefer updates over duplicates. Use when this capability is needed.
metadata:
  author: z3z1ma
---

<!-- BEGIN:compound:skill-managed -->
## What makes a skill “good”

- **Specific**: does one thing.
- **Procedural**: step list + checks, not philosophy.
- **Reusable**: applies to 2+ future contexts.
- **Low ceremony**: short enough to reread.

## Structure checklist

Include:

- Purpose / when to use
- Preconditions (tools, files, assumptions)
- Steps
- Examples (commands, snippets)
- Gotchas / failure modes
- Links to relevant docs or files (optional)

## Naming rules

Skill names must match:

- `^[a-z][a-z0-9]*(?:-[a-z0-9]+)*$`

Use kebab-case and keep it short.

## Updating an existing skill

Prefer updating when:

- This is a better version of the same procedure.
- The old skill is incomplete or wrong.
- You’re adding new edge cases.

Create a new skill when:

- The procedure is materially different.
- The new skill would be confusing if merged.

## Keep it true

No "hand-wavy" steps like "just fix it".
If a step requires a command, include the command.
<!-- END:compound:skill-managed -->

## Manual notes

_This section is preserved when the skill is updated. Put human notes, caveats, and exceptions here._

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/z3z1ma) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
