---
name: add-skill
description: Add a new skill to an existing Claude Code plugin. Use when user wants to create a skill. Use when this capability is needed.
metadata:
  author: nrempel
---

# Add Skill

You are adding a new skill to a Claude Code plugin. Follow these steps:

## 1. Gather Information

Ask the user for:
- **Plugin path** (which plugin to add to, or current directory)
- **Skill name** (lowercase, e.g., `my-skill`)
- **Description** (when should Claude use this skill?)
- **User-invocable** (show in `/` menu? default: true)
- **What should it do?** (the actual instructions)

## 2. Create Skill Directory

```
<plugin>/skills/<skill-name>/
└── SKILL.md
```

## 3. Generate SKILL.md

Use this format:

```markdown
---
name: <skill-name>
description: <description>. Use when <trigger condition>.
user-invocable: <true|false>
---

# <Skill Name>

<Instructions for Claude on how to execute this skill>

## Steps

1. <Step 1>
2. <Step 2>
3. ...

## Guidelines

- <Guideline 1>
- <Guideline 2>
```

## 4. Frontmatter Reference

Available frontmatter fields:
- `name` (required): Skill identifier
- `description` (required): When Claude should use this skill
- `user-invocable`: Show in `/` menu (default: true)
- `disable-model-invocation`: Block auto-invocation (default: false)
- `allowed-tools`: Restrict which tools the skill can use

## 5. Best Practices

- **Description matters**: Claude uses it to decide when to invoke
- **Be specific**: Include trigger conditions ("Use when...")
- **Structure instructions**: Use numbered steps, headers, examples
- **Keep it focused**: One skill = one capability

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nrempel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
