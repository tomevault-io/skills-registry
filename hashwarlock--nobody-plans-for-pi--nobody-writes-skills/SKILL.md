---
name: nobody-writes-skills
description: Use when creating new skills, editing existing skills, or verifying skills work before deployment
metadata:
  author: hashwarlock
---

# Writing Skills

## Overview

Writing skills IS Test-Driven Development applied to process documentation. Write test scenarios, watch agents fail without the skill, write the skill, watch them succeed.

## Skill Structure

```
skill-name/
├── SKILL.md              # Required: frontmatter + instructions
├── scripts/              # Helper scripts (optional)
└── references/           # Detailed docs (optional)
```

### SKILL.md Format

```markdown
---
name: skill-name
description: Use when [specific triggering conditions]
---

# Skill Name

## Overview
Core principle in 1–2 sentences.

## When to Use
Symptoms and use cases.

## Process
Steps to follow.

## Common Mistakes
What goes wrong + fixes.
```

### Frontmatter Rules

- `name`: lowercase, hyphens, 1–64 chars, must match directory name
- `description`: start with "Use when...", max 1024 chars, describe triggers NOT workflow

**Critical:** Description = WHEN to use, NOT what the skill does. Testing shows agents follow descriptions as shortcuts and skip the full skill body.

## Testing Skills (TDD)

### RED — Baseline
Run a pressure scenario with a subagent WITHOUT the skill. Document:
- What choices did they make?
- What rationalizations did they use?

### GREEN — Write Skill
Write skill addressing the specific failures observed. Run same scenario WITH skill — agent should now comply.

### REFACTOR — Close Loopholes
Agent found a new rationalization? Add explicit counter. Re-test.

## When to Create

**Do create for:** Reusable techniques, non-obvious patterns, cross-project workflows
**Don't create for:** One-off solutions, project-specific config, mechanically enforceable rules

## Checklist

- [ ] Baseline scenario run (RED)
- [ ] Skill addresses observed failures (GREEN)
- [ ] Loopholes plugged (REFACTOR)
- [ ] Name matches directory, description starts with "Use when..."
- [ ] Tested with subagent

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hashwarlock) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
