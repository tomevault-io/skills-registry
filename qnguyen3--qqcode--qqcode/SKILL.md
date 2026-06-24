---
name: qqcode
description: Use `skill` to activate domain-specific expertise when available. Use when this capability is needed.
metadata:
  author: qnguyen3
---
Use `skill` to activate domain-specific expertise when available.

**Arguments:**
- `name`: The name of the skill to activate (must match a skill from `<available_skills>`)

**When to Use:**
- Check the `<available_skills>` section in your system context for available skills
- Activate a skill when the user's request matches a skill's description
- Skills provide specialized workflows, context, and best practices for specific domains

**How Skills Work:**
1. Skills are discovered from `.qqcode/skills/[skill-name]/SKILL.md` directories
2. Each skill has a name and description listed in `<available_skills>`
3. When you call this tool with a skill name, you receive the full skill content
4. Follow the instructions in the skill content to complete the user's request

**Example:**
```
User: "Create a modern landing page with great design"

If <available_skills> contains a "frontend-design" skill, activate it:
skill(name="frontend-design")

Then follow the skill's instructions to create the landing page.
```

**Important:**
- Only activate skills that are listed in `<available_skills>`
- The skill content may reference other files in the skill's directory
- Skills can provide their own workflows, templates, and guidelines
- Once activated, follow the skill's instructions for the remainder of the task
- After activated, check out the triggered skill folder to find all related scripts, extra instructions, etc.

---
> Source: [qnguyen3/qqcode](https://github.com/qnguyen3/qqcode) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-22 -->
