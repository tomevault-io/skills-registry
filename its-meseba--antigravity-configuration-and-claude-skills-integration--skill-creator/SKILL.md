---
name: skill-creator
description: Guide for creating effective skills. This skill should be used when users want to create a new skill (or update an existing one) that extends the agent's abilities with specialized knowledge, workflows, or tool integrations. Use when this capability is needed.
metadata:
  author: its-meseba
---

# Skill Creator

This skill provides guidance for creating effective skills for Antigravity IDE. Skills created with this tool are **fully compatible with [Claude Code Skills](https://github.com/anthropics/skills)**.

## About Skills

Skills are modular, self-contained packages that extend the agent's abilities by providing specialized knowledge, workflows, and tools. They transform the agent from a general-purpose assistant into a specialized agent equipped with procedural knowledge for specific domains.

## Anatomy of a Skill

```
skill-name/
├── SKILL.md (required)
│   ├── YAML frontmatter (required)
│   │   ├── name: (required)
│   │   └── description: (required)
│   └── Markdown instructions
└── Bundled Resources (optional)
    ├── scripts/      - Executable code
    ├── references/   - Documentation for context
    └── assets/       - Templates, images, files
```

### Progressive Disclosure

| Level | Content | When Loaded |
|-------|---------|-------------|
| 1 | Metadata | Always (~100 words) |
| 2 | SKILL.md body | When triggered (<5k words) |
| 3 | Bundled resources | As needed (unlimited) |

## When to Use This Skill

- Creating a new skill from scratch
- Updating an existing skill
- Packaging a skill for distribution
- Understanding skill best practices

## Skill Creation Process

### Step 1: Understand with Examples

Gather concrete examples of how the skill will be used:
- "What functionality should this skill support?"
- "Can you give examples of how it would be used?"
- "What would trigger this skill?"

### Step 2: Plan Reusable Contents

For each example, analyze:
1. How to execute the task from scratch
2. What scripts, references, or assets would be helpful

**Example analyses:**

| Skill | Query | Analysis | Resource |
|-------|-------|----------|----------|
| pdf-editor | "Rotate this PDF" | Same code each time | `scripts/rotate_pdf.py` |
| frontend-builder | "Build a todo app" | Same boilerplate each time | `assets/hello-world/` |
| big-query | "How many users today?" | Need schema each time | `references/schema.md` |

### Step 3: Initialize

Run the initialization script:

```bash
scripts/init_skill.py <skill-name> --path ~/.gemini/skills/
```

This creates:
- `SKILL.md` with proper frontmatter and TODOs
- Example `scripts/`, `references/`, `assets/` directories
- Placeholder files to customize or delete

### Step 4: Edit the Skill

#### Start with Resources
1. Create scripts for repeated code
2. Add references for documentation
3. Include assets for templates
4. Delete unused example directories

#### Complete SKILL.md
Answer these questions:
1. **Purpose**: What does this skill do? (few sentences)
2. **When**: When should it be used? (trigger conditions)
3. **How**: How should it be used? (reference all resources)

#### Writing Style
- Use **imperative/infinitive form** (verb-first instructions)
- Write objectively: "To accomplish X, do Y" not "You should do X"
- Keep SKILL.md lean; move details to `references/`

### Step 5: Package (Optional)

For distributing skills:

```bash
scripts/package_skill.py <path/to/skill>
```

This validates and zips the skill.

### Step 6: Iterate

After using the skill:
1. Notice struggles or inefficiencies
2. Identify needed updates
3. Implement changes
4. Test again

## SKILL.md Best Practices

### Frontmatter Quality
The `name` and `description` determine when the agent uses the skill:
- Be specific about what it does
- Use third person: "This skill should be used when..."
- Include trigger keywords

### Structure Template

```markdown
---
name: skill-name
description: One-line description of what this skill does and when to use it.
---

# Skill Name

Brief description of purpose.

## When to Use This Skill

- Bullet list of use cases
- Trigger conditions

## What This Skill Does

1. First thing it does
2. Second thing it does
3. Third thing it does

## How to Use

### Basic Usage
\`\`\`
Example prompt
\`\`\`

### Advanced Usage
\`\`\`
More complex example
\`\`\`

## Example

Full example with input and output.

## Tips for Success

Best practices and gotchas.
```

## Resource Guidelines

### Scripts (`scripts/`)
- Include when code is rewritten repeatedly
- Benefits: Token efficient, deterministic, executed without loading
- May need to be read for patching or environment adjustments

### References (`references/`)
- Include for documentation the agent should reference
- Use cases: Schemas, API docs, policies, detailed guides
- Best practice: If large (>10k words), include grep patterns in SKILL.md

### Assets (`assets/`)
- Include for files used in output
- Use cases: Templates, images, boilerplate code
- Benefits: Used without loading into context

## Creating via Conversation

Instead of using scripts, you can ask:

```
Create a new skill called "X" that does Y. 
Include a script for Z and reference docs for W.
```

The agent will:
1. Create the directory structure
2. Write SKILL.md
3. Add requested resources
4. Update skills/README.md

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/its-meseba) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
