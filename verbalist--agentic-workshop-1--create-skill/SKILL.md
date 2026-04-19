---
name: create-skill
description: Guide for creating a new skill in the agentic-workflow marketplace. Use this skill to scaffold a new skill with proper structure, frontmatter, and documentation. Use when this capability is needed.
metadata:
  author: verbalist
---

# Create Marketplace Skill

Create a new skill in the agentic-workflow marketplace following conventions.

## Variables

- `skill_name`: $1 - Name of the skill to create (kebab-case)
- `category`: $2 - Category: sdlc, testing, operations, utility, integrations

## Instructions

1. Create directory: `skills/{category}/{skill_name}/`
2. Create `SKILL.md` with the following structure:

### Required YAML Frontmatter

```yaml
---
name: {skill_name}
description: Clear description of what this skill does and when to use it.
version: 1.0.0
---
```

### Required Sections

- **Title** (# heading matching skill name)
- **Variables** - Input parameters with descriptions
- **Instructions** - Step-by-step execution guide
- **Report** - Expected output format

### Optional Sections

- **Relevant Files** - Files the skill typically works with
- **Footprint and State Management** - If skill creates persistent artifacts
- **Examples** - Usage examples
- **Integration with Other Skills** - How it connects to the workflow

3. Update the catalog: `bash scripts/catalog-update.sh`

## Template

See `templates/skill/SKILL.md.template` for a blank starter.

## Naming Conventions

- Skill names: kebab-case (e.g., `my-new-skill`)
- Descriptions: Start with action verb, explain when to use
- Categories: Use existing categories when possible

## Report

After creating the skill, return:
```json
{
  "skill_path": "skills/{category}/{skill_name}/SKILL.md",
  "catalog_updated": true
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/verbalist) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
