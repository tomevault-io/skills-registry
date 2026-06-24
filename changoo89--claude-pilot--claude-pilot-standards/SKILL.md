---
name: claude-pilot-standards
description: Author reference for claude-pilot components. Skills, commands, agents, rules documentation patterns. VIBE coding compliance. Use when this capability is needed.
metadata:
  author: changoo89
---

# SKILL: Claude-Pilot Standards

> **Purpose**: Author reference for creating plugin components
> **Target**: Contributors creating skills, commands, agents

## Quick Reference

| Component | Location | Purpose | Size Limit |
|-----------|----------|---------|------------|
| **Skills** | `.claude/skills/{name}/` | Auto-discoverable capabilities | SKILL: 200, REF: 300 |
| **Commands** | `.claude/commands/` | Slash commands | 150 lines |
| **Agents** | `.claude/agents/` | Specialized configs | 200 lines |
| **Rules** | `.claude/rules/` | Delegation patterns | 200 lines |

## Component Taxonomy

### Skills
**Structure**: `{name}/SKILL.md` (≤200) + `REFERENCE.md` (≤300)

**Required**: `name`, `description` with trigger keywords

**Examples**: @.claude/skills/tdd/SKILL.md | @.claude/skills/vibe-coding/SKILL.md

### Commands
**Structure**: Single `.md` with description frontmatter

**Required**: `description` with action verbs + scenarios

**Examples**: @.claude/commands/00_plan.md | @.claude/commands/02_execute.md

### Agents
**Required**: `name`, `description`, `model`, `tools`

**Examples**: @.claude/agents/coder.md | @.claude/agents/plan-reviewer.md

### Rules
**Structure**: `core/`, `delegator/`, `documentation/`

**Examples**: @.claude/rules/core/workflow.md | @.claude/rules/delegator/triggers.md

## VIBE Coding Standards

| Target | Limit |
|--------|-------|
| Function | ≤50 lines |
| File | ≤200 lines |
| Nesting | ≤3 levels |

**Principles**: SRP, DRY, KISS, Early Return

**Full**: @.claude/skills/vibe-coding/SKILL.md

## Cross-References

**Format**: `@.claude/{path}/{file}`

**Best**: Absolute paths, specific files, descriptive text

## Frontmatter Patterns

**Skill**: `name`, `description` (trigger keywords)
**Command**: `description` (action verbs + scenarios)
**Agent**: `name`, `description`, `model`, `tools`

## Common Patterns

**Methodology extraction**:
```markdown
> **Methodology**: @.claude/skills/tdd/SKILL.md
```

**MANDATORY marker**:
```markdown
> **⚠️ MANDATORY ACTION**: YOU MUST {action} NOW
```

**Completion markers**: `<AGENT_COMPLETE>`, `<AGENT_BLOCKED>`

## Further Reading

**Internal**: @.claude/skills/claude-pilot-standards/REFERENCE.md | @.claude/skills/claude-pilot-standards/TEMPLATES.md | @.claude/skills/claude-pilot-standards/EXAMPLES.md

**External**: [Claude Code Best Practices](https://www.anthropic.com/engineering/claude-code-best-practices)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/changoo89) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
