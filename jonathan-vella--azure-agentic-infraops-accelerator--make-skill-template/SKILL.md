---
name: make-skill-template
description: Scaffolds new Agent Skills with SKILL.md frontmatter, folder structure, and bundled resources. USE FOR: create a skill, scaffold skill, new skill template, add agent capability. DO NOT USE FOR: Azure infrastructure, Bicep/Terraform code, architecture decisions. Use when this capability is needed.
metadata:
  author: jonathan-vella
---

# Make Skill Template

A meta-skill for creating new Agent Skills. Use this skill when you need to scaffold
a new skill folder, generate a SKILL.md file, or help users understand the Agent Skills
specification.

## When to Use This Skill

- User asks to "create a skill", "make a new skill", or "scaffold a skill"
- User wants to add a specialized capability to their GitHub Copilot setup
- User needs help structuring a skill with bundled resources
- User wants to duplicate this template as a starting point

## Prerequisites

- Understanding of what the skill should accomplish
- A clear, keyword-rich description of capabilities and triggers
- Knowledge of any bundled resources needed (scripts, references, assets, templates)

## Creating a New Skill

📋 **Reference**: Read `references/step-by-step-guide.md` for the detailed 4-step creation process:

1. **Create the Skill Directory** — lowercase, hyphenated folder name
2. **Generate SKILL.md with Frontmatter** — field requirements, description best practices
3. **Write the Skill Body** — recommended sections and structure
4. **Add Optional Directories** — scripts/, references/, assets/, templates/

## Example: Complete Skill Structure

```text
my-awesome-skill/
├── SKILL.md                    # Required instructions
├── LICENSE.txt                 # Optional license file
├── scripts/
│   └── helper.py               # Executable automation
├── references/
│   ├── api-reference.md        # Detailed docs
│   └── examples.md             # Usage examples
├── assets/
│   └── diagram.png             # Static resources
└── templates/
    └── starter.ts              # Code scaffold
```

## Quick Start: Duplicate This Template

1. Copy the `make-skill-template/` folder
2. Rename to your skill name (lowercase, hyphens)
3. Update `SKILL.md`:
   - Change `name:` to match folder name
   - Write a keyword-rich `description:`
   - Replace body content with your instructions
4. Add bundled resources as needed
5. Validate with `npm run skill:validate`

## Validation Checklist

- [ ] Folder name is lowercase with hyphens
- [ ] `name` field matches folder name exactly
- [ ] `description` is 10-1024 characters
- [ ] `description` explains WHAT and WHEN
- [ ] `description` is wrapped in single quotes
- [ ] Body content is under 500 lines
- [ ] Bundled assets are under 5MB each

## Troubleshooting

| Issue                    | Solution                                                 |
| ------------------------ | -------------------------------------------------------- |
| Skill not discovered     | Improve description with more keywords and triggers      |
| Validation fails on name | Ensure lowercase, no consecutive hyphens, matches folder |
| Description too short    | Add capabilities, triggers, and keywords                 |
| Assets not found         | Use relative paths from skill root                       |

## Project-Specific Scaffold Templates

📋 **Reference**: Read `references/scaffold-templates.md` for Azure Knowledge Skill
and Integration Skill skeleton templates,
plus the "Before Committing a New Skill" checklist.

## References

- Agent Skills official spec: <https://agentskills.io/specification>

## Reference Index

Load these on demand — do NOT read all at once:

| Reference                          | When to Load                                                   |
| ---------------------------------- | -------------------------------------------------------------- |
| `references/step-by-step-guide.md` | Detailed 4-step skill creation process with frontmatter rules  |
| `references/scaffold-templates.md` | Azure/Integration skill skeletons, before-committing checklist |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jonathan-vella) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
