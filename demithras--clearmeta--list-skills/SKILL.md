---
name: list-skills
description: List all available skills with their descriptions. Use: /list-skills Use when this capability is needed.
metadata:
  author: demithras
---

# /list-skills — Skill Discovery

> **TL;DR**: Show all available skills across project and global scopes.

---

## Usage

```
/list-skills                # List all skills (project + global)
/list-skills --scope=project  # Only project skills (.claude/skills/)
/list-skills --scope=global   # Only global skills (~/.claude/skills/)
/list-skills --all          # Include experimental skills
```

---

## Scope Hierarchy

| Scope | Location | Priority |
|-------|----------|----------|
| **Project** | `.claude/skills/` | Higher (overrides global) |
| **Global** | `~/.claude/skills/` | Lower (default fallback) |

**Override rule**: If same skill name exists in both scopes, project version wins.

---

## Execution

```
1. PARSE flags:
   scope_filter = PARSE --scope flag (default: "all")
   include_experimental = PARSE --all flag (default: false)

2. COLLECT skills:
   skills = []

   # Global skills first (lower priority)
   IF scope_filter IN ["all", "global"]:
       FOR EACH path IN Glob("~/.claude/skills/*/SKILL.md"):
           IF "_experimental" IN path AND NOT include_experimental:
               CONTINUE
           skill = PARSE_SKILL(path)
           skill.scope = "global"
           skills.push(skill)

   # Project skills second (higher priority, override global)
   IF scope_filter IN ["all", "project"]:
       FOR EACH path IN Glob(".claude/skills/*/SKILL.md"):
           IF "_experimental" IN path AND NOT include_experimental:
               CONTINUE
           skill = PARSE_SKILL(path)
           skill.scope = "project"

           # Check for override
           existing = skills.find(s => s.name == skill.name)
           IF existing:
               existing.overridden = true
               skill.overrides = existing.name

           skills.push(skill)

3. PARSE_SKILL(path):
   content = Read(path)
   frontmatter = EXTRACT YAML between ---
   RETURN {
       name: frontmatter.name,
       description: frontmatter.description,
       version: frontmatter.version,
       path: path,
       scope: null  # set by caller
   }

4. OUTPUT formatted table with scope indicators
```

---

## Output Format

```markdown
## Available Skills

| Skill | Scope | Description | Version |
|-------|-------|-------------|---------|
| /meta | 📁 project | Strategic decision analysis | 3.0.0 |
| /skill-check | 📁 project | Validate skill quality | 1.0.0 |
| /retro | 📁 project | Session retrospective | 2.1.0 |
| /commit | 🌐 global | Git commit helper | 1.0.0 |
| /review | 🌐 global | Code review | 1.0.0 |

**Total**: 3 project + 2 global = 5 skills

### Overrides
| Project Skill | Overrides Global |
|---------------|------------------|
| /meta | ~/.claude/skills/meta |
```

---

## Scope Indicators

| Icon | Meaning |
|------|---------|
| 📁 | Project scope (`.claude/skills/`) |
| 🌐 | Global scope (`~/.claude/skills/`) |
| ⚠️ | Overridden (global skill hidden by project) |

---

## Notes

- Skills in `_experimental/` hidden by default (use `--all`)
- Project skills take precedence over global with same name
- Use `/skill-check <name>` to validate any skill
- Use `/skill-farm` to create or improve skills

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/demithras) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
