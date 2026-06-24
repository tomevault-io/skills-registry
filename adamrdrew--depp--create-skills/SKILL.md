---
name: create-skills
description: How to create new skills to refer back to later. This is a great, structured way to learn and grow your abilities! Use when this capability is needed.
metadata:
  author: adamrdrew
---

# How to Create Skills

## What Skills Are
Skills are persistent how-to documents about capabilities — things you can DO. They capture reusable techniques, tool usage patterns, search strategies, document formats, and other procedural knowledge. If you learn how to do something non-trivial that you'll likely do again, make it a skill.

Skills are NOT for project-specific notes, research findings, observations, or contextual knowledge. That stuff goes in your notebook (index.md). The distinction: skills teach future-you HOW to do things; notebook entries tell future-you WHAT you know about things.

## Structure
Each skill is a self-contained folder under `.claude/skills/`:

```
.claude/skills/
  skill-name-slug/
    SKILL.md             <-- The skill doc (YAML frontmatter + markdown)
    helper-script.sh     <-- Any associated files colocated here
    template.md          <-- Templates, examples, etc.
```

All files associated with a skill live in its folder. Keep things colocated.

## Steps to Create a Skill
1. Create the folder: `.claude/skills/your-skill-name/`
2. Write `SKILL.md` with frontmatter and content using the Write tool
3. Colocate any associated files (scripts, templates) in the same folder

## How Claude Code Discovers Skills
Claude Code automatically discovers skills in `.claude/skills/`. Each skill needs:
- A directory under `.claude/skills/`
- A `SKILL.md` file with YAML frontmatter containing `name` and `description`

Once created, skills appear in the system reminder at the start of every conversation. They can be invoked with the `Skill` tool, which loads the skill content into context. No manual registration needed — just create the files and they're live.

## Tips
- Be kind to future-you: write clearly, include examples, note pitfalls
- If a skill has helper scripts or templates, put them in the skill folder
- Keep descriptions concise but specific enough to trigger recall
- The `description` in frontmatter is what Claude Code shows in the skill list — make it useful for deciding when to invoke the skill

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adamrdrew) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
