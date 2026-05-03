---
name: skill-generator
description: Creates new Antigravity skills with streamlined workflows and automation. Use when users want to create a new skill, improve an existing skill, or need guidance on skill structure. Triggers include "create a skill", "make a new skill", "build a skill for", "skill for [task]", "help me create a skill", or any request to extend Antigravity's capabilities with specialized knowledge or workflows.
metadata:
  author: ericzhou0815
---

# Skill Generator

Create effective Antigravity skills quickly with clear workflows, automation scripts, and ready-to-use templates.

## Quick Start (5 Steps)

Get from idea to working skill in 10 minutes:

1. **Initialize**: Run `python scripts/init_skill.py <skill-name> --path .agent/skills`
2. **Edit frontmatter**: Update `name` and `description` in SKILL.md (critical for triggering)
3. **Write instructions**: Add your skill's core guidance (use imperative form)
4. **Clean up**: Delete unused example files from `scripts/`, `references/`, `assets/`
5. **Package**: Run `python scripts/package_skill.py .agent/skills/<skill-name>`

**Done!** You now have a `.skill` file ready to share.

---

## Skill Creation Workflow

### Step 1: Understand the Need

Before creating a skill, clarify:
- **What task** does this skill enable?
- **When should it trigger?** (specific user requests, file types, scenarios)
- **What makes it reusable?** (repeated workflows, specialized knowledge, automation)

**Example questions to ask**:
- "What functionality should this skill support?"
- "Can you give examples of how it would be used?"
- "What would a user say to trigger this skill?"

### Step 2: Plan Reusable Contents

Analyze what resources would help when executing this task repeatedly:

**Scripts** (`scripts/`) - Use when:
- Same code is rewritten repeatedly
- Deterministic reliability needed
- Complex operations (PDF manipulation, API calls, data processing)

**References** (`references/`) - Use when:
- Detailed documentation needed (API docs, schemas, policies)
- Information too lengthy for SKILL.md
- Content only needed for specific use cases

**Assets** (`assets/`) - Use when:
- Templates or boilerplate needed (PowerPoint, HTML, React projects)
- Images, fonts, or media files required
- Files used in output (not loaded into context)

**Instruction-only** - Use when:
- Workflow guidance is sufficient
- No repeated code or templates
- Simple procedural knowledge

### Step 3: Initialize the Skill

Run the initialization script:

```bash
python scripts/init_skill.py <skill-name> --path .agent/skills
```

**Naming requirements**:
- Hyphen-case (e.g., `pdf-editor`, `nz-job-search`)
- Lowercase letters, digits, hyphens only
- Max 40 characters
- Must match directory name

This creates:
```
.agent/skills/<skill-name>/
├── SKILL.md (with TODO placeholders)
├── scripts/example.py
├── references/api_reference.md
└── assets/example_asset.txt
```

### Step 4: Write SKILL.md

#### Frontmatter (Critical)

```yaml
---
name: skill-name
description: [Complete description of what the skill does AND when to use it]
---
```

**Description best practices**:
- Include WHAT the skill does
- Include WHEN to trigger (specific scenarios, file types, keywords)
- List example trigger phrases
- Be comprehensive - this determines when the skill loads

**Example**:
```yaml
description: Creates and edits PDF documents with support for forms, annotations, and text extraction. Use when working with PDF files for: (1) Filling forms, (2) Extracting text, (3) Merging/splitting, (4) Adding annotations. Triggers: "edit this PDF", "fill PDF form", "extract text from PDF", "merge PDFs".
```

#### Body Structure

Choose a structure that fits your skill:

**Workflow-Based** (sequential processes):
```markdown
## Overview
## Workflow Decision Tree
## Step 1: [Action]
## Step 2: [Action]
```

**Task-Based** (tool collections):
```markdown
## Overview
## Quick Start
## Task 1: [Capability]
## Task 2: [Capability]
```

**Reference-Based** (standards/guidelines):
```markdown
## Overview
## Guidelines
## Specifications
## Usage Examples
```

**Writing guidelines**:
- Use imperative form ("Create", "Run", "Check" not "Creating", "You should")
- Keep SKILL.md under 500 lines (split into references if longer)
- Include concrete examples
- Reference bundled resources clearly
- Delete the template guidance sections

### Step 5: Add Bundled Resources

#### Scripts

Test all scripts by running them:
```bash
python scripts/your_script.py
```

Ensure they work without errors. Scripts should be self-contained and documented.

#### References

For files >100 lines, add a table of contents at the top. Structure references for easy scanning.

**Progressive disclosure**: Link to references from SKILL.md only when needed:
```markdown
For advanced features, see [advanced.md](references/advanced.md)
```

#### Assets

Store templates, images, fonts, or boilerplate that will be used in output. These are NOT loaded into context.

**Delete unused directories** - Not every skill needs all three types.

### Step 6: Validate & Package

Before packaging, validate:

```bash
python scripts/quick_validate.py .agent/skills/<skill-name>
```

Fix any errors, then package:

```bash
python scripts/package_skill.py .agent/skills/<skill-name>
```

This creates `<skill-name>.skill` - a distributable zip file.

**Validation checklist**:
- ✅ YAML frontmatter has `name` and `description`
- ✅ Description includes "when to use" triggers
- ✅ Instructions use imperative form
- ✅ Unused example files deleted
- ✅ Scripts tested and working
- ✅ SKILL.md under 500 lines
- ✅ References linked from SKILL.md

---

## Skill Design Principles

### 1. Concise is Key

Context window is shared. Only include what Claude doesn't already know.

**Challenge each section**: "Does Claude really need this?" and "Does this justify its token cost?"

Prefer examples over explanations.

### 2. Progressive Disclosure

Skills load in three levels:
1. **Metadata** (name + description) - Always in context
2. **SKILL.md body** - When skill triggers
3. **Bundled resources** - As needed

Keep SKILL.md lean. Move detailed content to references.

### 3. Set Appropriate Freedom

Match specificity to task fragility:
- **High freedom** (text instructions): Multiple valid approaches
- **Medium freedom** (pseudocode): Preferred pattern with variation
- **Low freedom** (specific scripts): Fragile operations requiring exact steps

---

## Templates & Examples

### Ready-to-Use Templates

See [references/skill-templates.md](references/skill-templates.md) for copy-paste templates:
- Instruction-only skill
- Script-based skill
- Reference-heavy skill
- Tool integration skill

### Real Skill Examples

See [references/skill-examples.md](references/skill-examples.md) for complete, annotated examples showing different patterns.

### Design Patterns

See existing reference docs for proven patterns:
- [references/workflows.md](references/workflows.md) - Sequential workflows and conditional logic
- [references/output-patterns.md](references/output-patterns.md) - Template and example patterns

---

## Common Patterns

### Multi-Framework Skills

When supporting multiple frameworks/variants, organize by variant:

```
skill-name/
├── SKILL.md (overview + selection guide)
└── references/
    ├── framework-a.md
    ├── framework-b.md
    └── framework-c.md
```

Claude loads only the relevant reference when the user chooses a framework.

### Domain-Specific Organization

For skills with multiple domains:

```
bigquery-skill/
├── SKILL.md (navigation)
└── references/
    ├── finance.md
    ├── sales.md
    └── product.md
```

Claude loads only the relevant domain reference.

### Conditional Details

Show basic content, link to advanced:

```markdown
## Basic Usage
[Simple instructions]

**For advanced features**: See [advanced.md](references/advanced.md)
```

---

## Troubleshooting

**Skill not triggering?**
- Check `description` includes trigger phrases
- Add more "when to use" scenarios
- Include specific keywords users might say

**SKILL.md too long?**
- Move detailed content to `references/`
- Keep only essential workflow in SKILL.md
- Use progressive disclosure pattern

**Scripts not working?**
- Test scripts directly before packaging
- Ensure all dependencies documented
- Add error handling and clear output

**Validation failing?**
- Check YAML frontmatter format
- Ensure `name` matches directory name
- Verify `description` is comprehensive

---

## What NOT to Include

Do not create extraneous documentation:
- README.md
- INSTALLATION_GUIDE.md
- CHANGELOG.md
- QUICK_REFERENCE.md

Skills are for AI agents, not humans. Include only what's needed for the task.

---

## Iteration

After using the skill:
1. Notice struggles or inefficiencies
2. Identify needed improvements
3. Update SKILL.md or resources
4. Re-package and test

Skills improve through real usage.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ericzhou0815) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
