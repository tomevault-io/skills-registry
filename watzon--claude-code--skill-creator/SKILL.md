---
name: skill-creator
description: | Use when this capability is needed.
metadata:
  author: watzon
---

# Skill Creator

Create effective, well-structured skills for Claude Code and OpenCode.

## Quick Start

1. **Run init script**: `bash scripts/init_skill.sh <skill-name> [output-dir]`
2. **Edit SKILL.md**: Fill in frontmatter and instructions
3. **Add resources**: scripts/, references/, assets/ as needed
4. **Test**: Run skill in context to verify behavior

## Skill Anatomy

```
skill-name/
├── SKILL.md              # Required - frontmatter + instructions
├── scripts/              # Executable code (Python/Bash)
├── references/           # Documentation loaded on-demand
└── assets/               # Templates, images, static files
```

**Frontmatter** (YAML) - only two fields:
- `name`: Lowercase, hyphens, max 64 chars (must match directory name)
- `description`: What it does + when to use it, max 1024 chars

For complete structure details, see [references/skill-anatomy.md](references/skill-anatomy.md).

## Core Workflow

### Step 1: Define Purpose

Answer these questions:
- What problem does this skill solve?
- What triggers should activate it? (error messages, keywords, scenarios)
- What does Claude need to know that isn't obvious?

### Step 2: Initialize Skill

```bash
# Create new skill with proper structure
bash scripts/init_skill.sh my-skill-name ~/.claude/skills

# Or manually:
mkdir -p ~/.claude/skills/my-skill-name
touch ~/.claude/skills/my-skill-name/SKILL.md
```

### Step 3: Write Frontmatter

```yaml
---
name: my-skill-name
description: |
  Use when [specific triggers and symptoms].
  Provides [what it does] for [use case].
---
```

**Critical**: Description is the PRIMARY trigger mechanism. Include:
- Concrete triggers ("Use when...")
- Problem symptoms (error messages, scenarios)
- Keywords Claude might search for

### Step 4: Write Body

**Keep SKILL.md lean** (<500 lines, ~1500-2000 words):
- Overview: Core principle in 1-2 sentences
- Quick reference: Table/bullets for scanning
- Workflow steps: What to do, in order
- Pointers to references/ for detailed content

### Step 5: Add Resources (Optional)

| Type | Location | When to Use |
|------|----------|-------------|
| Scripts | `scripts/` | Deterministic operations, reusable code |
| References | `references/` | Detailed docs, API specs, schemas |
| Assets | `assets/` | Templates, images, static files |

### Step 6: Test and Iterate

1. Use the skill in a real scenario
2. Note what's unclear or missing
3. Update and re-test
4. Repeat until bulletproof

## Progressive Disclosure

Skills use three-level loading to conserve context:

| Level | What Loads | When |
|-------|------------|------|
| Metadata | name + description (~100 words) | Always in context |
| SKILL.md body | Instructions (<5k words) | When skill triggers |
| Resources | scripts/, references/, assets/ | When explicitly needed |

**Key principle**: Only load what's needed. Reference files from SKILL.md clearly:

```markdown
For API details, see [references/api.md](references/api.md).
For form handling, see [references/forms.md](references/forms.md).
```

## Description Writing

The description field is CRITICAL for skill discovery.

**Pattern**: `Use when [triggers] - [what it provides]`

```yaml
# Bad - vague, no triggers
description: Helps with testing

# Bad - implementation details, not triggers
description: Uses Jest and React Testing Library

# Good - specific triggers + value
description: |
  Use when tests fail intermittently, have race conditions, or 
  timing dependencies - provides condition-based polling patterns
  that replace arbitrary timeouts.
```

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Everything in SKILL.md | Move detailed content to references/ |
| Vague description | Add specific triggers and symptoms |
| No "Use when" triggers | Description must tell Claude WHEN to use it |
| Deeply nested references | Keep references one level from SKILL.md |
| Missing cross-references | Point to references/ files explicitly |

## Resources

- [references/skill-anatomy.md](references/skill-anatomy.md) - Complete structure documentation
- [references/best-practices.md](references/best-practices.md) - Anthropic guidelines and patterns
- [references/examples.md](references/examples.md) - Annotated skill examples
- [scripts/init_skill.sh](scripts/init_skill.sh) - Skill scaffolding script
- [assets/skill-template.md](assets/skill-template.md) - Copy-paste starter template

## Checklist

Before deploying a skill:

- [ ] `name` in frontmatter matches directory name
- [ ] `description` starts with "Use when..." and includes triggers
- [ ] SKILL.md body is <500 lines
- [ ] Detailed content moved to references/
- [ ] References/ files linked from SKILL.md
- [ ] Tested in real scenario

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/watzon) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
