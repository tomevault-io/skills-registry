---
name: rules-manager
description: Manages practice rules. Use when user states a preference or approach, or asks to add/modify rules for coding, architecture, tooling, or best practices.
metadata:
  author: fturkyilmaz
---

# furkan-rules: Development Practices Manager

Manages development rules across skills. Before working, apply all rules in
`.claude/skills/*/SKILL.md`.

## Quick Start

1. Identify scope → choose skill (or create new)
2. Add/update rule in `.claude/skills/<name>/references/rules.md`
3. Update `.claude/skills/<name>/SKILL.md` key principles if needed
4. Validate: `npx -y claude-skills-cli validate .claude/skills/<name>`

## Skill Format

- **SKILL.md**: <50 lines, Quick Start + Key Principles + References
- **references/**: Detailed rules with Correct/Incorrect examples
- **Frontmatter**: `name` (kebab-case), `description` (<1024 chars)

## Skills by Scope

`architecture-guidelines`, `design-principles`, `coding-practices`,
`javascript-practices`, `tooling-standards`, `go-practices`,
`security-practices`, `workflow-practices`, `ci-cd-practices`

## References

See [skill-format.md](references/skill-format.md) for complete skill format
requirements.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fturkyilmaz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
