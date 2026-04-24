---
name: skill-builder
description: Use this skill when creating new Claude Code skills from scratch, editing existing skills to improve their descriptions or structure, or converting Claude Code sub-agents to skills. This includes designing skill workflows, writing SKILL.md files, organizing supporting files with intention-revealing names, and leveraging CLI tools and Node.js scripting.
metadata:
  author: mbcoalson
---

# Skill Builder

## Documentation References

Fetch these when you need to verify current behavior or best practices:
- https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills
- https://docs.claude.com/en/docs/agents-and-tools/agent-skills/overview
- https://docs.claude.com/en/docs/agents-and-tools/agent-skills/best-practices
- https://docs.claude.com/en/docs/claude-code/sub-agents

## Skill Anatomy

**Directory structure:**
```
skill-name/
├── SKILL.md (required)
├── intention-revealing-name.md (optional supporting files)
└── scripts/ (optional, Node.js preferred)
```

**Locations:**
- Personal: `~/.claude/skills/` — available across all projects
- Project: `.claude/skills/` — project-specific

**Template:** See `./templates/skill-template.md`

## Standards

**Name:** Gerund form, lowercase, hyphens only, max 64 chars.
- Good: `processing-pdfs`, `analyzing-spreadsheets`
- Bad: `pdf-helper`, `spreadsheet-utils`

**Description:** Most critical field — determines when Claude invokes the skill.
- Third person, under 1024 chars
- Lead with "Use this skill when..." and include trigger keywords
- Think from Claude's perspective: "When would I know to use this?"

**No `allowed-tools` field** — skills inherit all Claude Code CLI capabilities.

**Scripting:** Node.js (v24+, ESM imports, `.js` files). No Python, no TypeScript.

**Supporting files:** Use intention-revealing names (`aws-lambda-patterns.md`, not `helpers.md`). Reference with relative paths.

**Length:** Keep SKILL.md under 500 lines. Use progressive disclosure — move detail to supporting files.

## Creating a Skill

1. **Gather requirements** — What task does it handle? When should it be invoked? Personal or project?
2. **Design** — Choose a gerund name, draft the description, plan supporting files
3. **Create** — Write SKILL.md, add supporting files, write any Node.js scripts needed
4. **Add next steps capability** — Include the `add-skill-next-steps.js` pattern so WCC can surface progress. See `.claude/skills/work-command-center/skill-next-steps-convention.md`
5. **Validate:**
   - Gerund name, max 64 chars
   - Description is third person, trigger-focused, under 1024 chars
   - No `allowed-tools` field
   - Supporting files have intention-revealing names
   - No Python scripts

## Editing a Skill

- **Description first** — most edits should start here; better descriptions fix most invocation problems
- **Apply progressive disclosure** — move detail to supporting files, keep SKILL.md as the overview
- **Eliminate duplication** — if the same rule appears twice, it belongs once
- **Modernize scripts** — replace Python with Node.js equivalents where applicable

## Converting Sub-Agents to Skills

See `./converting-sub-agents-to-skills.md` for full guidance.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mbcoalson) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
