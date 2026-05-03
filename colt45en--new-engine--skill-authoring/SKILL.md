---
name: skill-authoring
description: Create, structure, and package Codex-compatible skills with SKILL.md frontmatter, progressive disclosure design, and optional scripts/references/assets. Use when defining a new skill folder layout, writing SKILL.md instructions, splitting large guidance into references, or preparing deterministic scripts for repeatable workflows. Use when this capability is needed.
metadata:
  author: colt45en
---

# Skill Authoring

## Default workflow

1. Identify the task the skill must enable, then list 3–5 concrete user examples the skill should handle.
2. Decide degrees of freedom (high/medium/low) based on fragility and variability.
3. Create the skill folder using lowercase letters, digits, and hyphens only (max 64 chars).
4. Write SKILL.md frontmatter with only `name` and `description`.
5. Keep SKILL.md body under ~500 lines; move deep details into `references/`.
6. Add scripts in `scripts/` when repeatability or deterministic reliability is needed.
7. Add `assets/` only for files used in final outputs (templates, icons, boilerplate).
8. Package the skill using the standard packaging script.

## Degrees of freedom selection

- High freedom: text-based guidance, multiple valid approaches
- Medium freedom: pseudocode/scripts with parameters, preferred patterns
- Low freedom: exact sequences, fragile operations, minimal variability

For the full spec and examples, read `references/skill-design.md`.

## Structure

Required:

- `SKILL.md` with frontmatter + instructions

Optional:

- `scripts/` for deterministic utilities
- `references/` for large docs, schemas, examples
- `assets/` for templates and files used in outputs

## Progressive disclosure

- Keep SKILL.md lean.
- Link to references directly from SKILL.md.
- Avoid deep nesting of references.
- Put large tables and extended examples into references files.

## Packaging

Follow the packaging section in `references/skill-design.md`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/colt45en) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
