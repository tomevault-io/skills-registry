---
name: metaskill-authoring
description: Write Claude Code skills and SKILL.md files. Use when creating new skills, writing skill content, structuring SKILL.md, organizing skill directories, or when user mentions "write skill", "create skill", "author skill", "new skill", "skill structure", "SKILL.md", "skill content", "skill template". Use when this capability is needed.
metadata:
  author: gigaverse-app
---

# Writing Great Claude Code Skills

## First: Two Key Questions

> **1. What should I name this?**
> See `/metaskill-naming` for the naming convention and brainstorming process.
>
> **2. Single skill or skill group?**
> Does this topic have 3+ distinct concerns users might approach from different angles?
> If YES, you need a **skill GROUP** (multiple skills in a plugin), not a single skill.
> See `/metaskill-grouping` for the skill group pattern.

## Skill Directory Structure

### Simple Skill (Single Concern)

```
skill-name/
  SKILL.md
```

### Skill with Deep Content

```
skill-name/
  SKILL.md           <- Quick answers and rules
  references/        <- Deep dives (OPTIONAL reading)
    topic-a.md
    topic-b.md
```

### Skill Group (Multiple Concerns)

```
plugin-name/
  .claude-plugin/
    plugin.json
  skills/
    plugin-name-doing/      <- ends in -ing
      SKILL.md
    plugin-name-other-ing/  <- ends in -ing
      SKILL.md
  README.md
```

**Note:** See `/metaskill-naming` for naming conventions (neutral noun prefix, -ing suffix for skills).

## SKILL.md Structure

### Required Frontmatter

```yaml
---
name: skill-name
description: <What it does>. Use when <specific actions>. Triggers on "<keyword1>", "<keyword2>", "<keyword3>", or when <conditions>.
---
```

The description is CRITICAL - it's the only field that affects triggering. See `/metaskill-triggering` for optimization techniques.

### Optional Frontmatter Fields

```yaml
---
name: skill-name
description: ...
allowed-tools: Read, Edit, Bash(npm test:*)
disable-model-invocation: true  # Blocks auto-triggering (user must invoke)
---
```

### Content Structure Template

```markdown
# Skill Title

## Core Philosophy (1-2 sentences)
What problem does this skill solve? What's the key insight?

## Quick Start
The 80% case - what users need most often.

## Critical Rules
Essential dos and don'ts as code blocks:
```python
# ✅ CORRECT
good_example()

# ❌ INCORRECT
bad_example()
```

## Checklist
Before completing, verify:
- [ ] Item 1
- [ ] Item 2

## Reference Files (if using references/)
For detailed patterns:
- [references/topic-a.md](references/topic-a.md) - Deep dive on A
- [references/topic-b.md](references/topic-b.md) - Deep dive on B

## Related Skills
- For X, see `/related-skill-a`
- For Y, see `/related-skill-b`
```

## Progressive Disclosure

**Principle:** Quick answers in SKILL.md body, deep dives in references.

| Location | Content Type | Reading |
|----------|-------------|---------|
| SKILL.md body | Rules, quick start, checklists | MANDATORY |
| references/ | Deep explanations, many examples | OPTIONAL |

**Warning:** Don't put critical rules ONLY in references - they might be skipped!

## When to Use References vs Skill Groups

**Use `references/` when:**
- Content supports the main skill but isn't required
- Users who trigger the skill need the SAME core content
- Deep dives are nice-to-have, not essential

**Use a skill group when:**
- Content is independently triggerable (different keywords)
- Users might ONLY want a subset
- Each area has substantial, distinct content

Example:
```
# references/ appropriate
testing/
  SKILL.md              <- All testers need this
  references/
    mocking.md          <- Some need deep mocking info
    fixtures.md         <- Some need deep fixture info

# Skill group appropriate (note: neutral prefix, -ing suffix)
metaskill/
  skills/
    metaskill-authoring/   <- "write skill"
    metaskill-triggering/  <- "fix trigger" (different concern!)
    metaskill-grouping/    <- "create plugin" (different concern!)
```

## Trigger Optimization

Your skill is useless if it never triggers. Key points:
- ONLY the `description` field affects triggering
- Include exact phrases users say
- Include action verbs (Create, Write, Generate)
- Include keyword variants (test, tests, testing)

For comprehensive trigger optimization, see `/metaskill-triggering`.

## Common Mistakes

**Body too long:** Extract deep content to references/

**Critical rules in references:** Keep essential rules in SKILL.md body

**Weak description:** See `/metaskill-triggering` for optimization

**Monolithic skill for complex topic:** See `/metaskill-grouping` for when to split

**Bad naming:** See `/metaskill-naming` for the naming convention

## Related Skills

- For naming conventions, see `/metaskill-naming`
- For trigger optimization, see `/metaskill-triggering`
- For skill group pattern, see `/metaskill-grouping`
- For plugin packaging and placement, see `/metaskill-packaging`
- To test triggers, use the `metaskill-trigger-tester` agent

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gigaverse-app) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
