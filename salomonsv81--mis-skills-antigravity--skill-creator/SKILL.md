---
name: skill-creator
description: Create new skills for the Antigravity IDE. Use when: creating skills, skill development, skill architecture. Use when this capability is needed.
metadata:
  author: salomonsv81
---

# Skill Creator

Guide for creating effective skills for Claude/AI assistants.

## Skill Structure

```
skill-name/
├── SKILL.md              # Required: Main skill definition
├── scripts/              # Optional: Automation scripts
├── docs/                 # Optional: Extended documentation
├── examples/             # Optional: Usage examples
└── resources/            # Optional: Templates, assets
```

## SKILL.md Format

```markdown
---
name: skill-name
description: "Clear description of what this skill does. Use when: trigger keywords."
---

# Skill Title

You are an expert in [domain]. Your capabilities include:

## Capabilities
- Capability 1
- Capability 2

## Patterns
### Pattern Name
[Code or workflow pattern]

## Anti-Patterns
- What NOT to do

## Best Practices
1. Practice 1
2. Practice 2
```

## Design Principles

1. **Focused**: One clear purpose per skill
2. **Actionable**: Include runnable examples
3. **Complete**: Provide full context needed
4. **Maintainable**: Keep content current

## Skill Categories

- **Framework**: React, Next.js, FastAPI
- **Infrastructure**: Docker, Kubernetes, Terraform
- **Workflow**: Git, CI/CD, Testing
- **Domain**: Authentication, API Design, Database

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/salomonsv81) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
