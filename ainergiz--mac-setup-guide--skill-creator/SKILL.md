---
name: skill-creator
description: Create new Claude Code skills. Use when user wants to create, update, or improve a skill that extends Claude's capabilities with specialized knowledge, workflows, or tool integrations. Use when this capability is needed.
metadata:
  author: ainergiz
---

# Skill Creator

Create skills that extend Claude's capabilities with specialized knowledge, workflows, and tools.

## What Skills Are

Skills are folders containing a `SKILL.md` file that teaches Claude how to do something specific. When a request matches a skill's description, Claude automatically applies it.

## Skill Locations

**IMPORTANT**: Before creating a skill, use `AskUserQuestion` to clarify:
- **Project skill** (`.claude/skills/`) - Shared with team via git, specific to this repo
- **Personal skill** (`~/.claude/skills/`) - Available across all your projects

If unclear, ask: "Should this skill be project-specific (shared with team) or personal (just for you)?"

## Skill Structure

```
skill-name/
├── SKILL.md              # Required - metadata + instructions
├── references/           # Optional - detailed docs loaded on-demand
│   └── guide.md
└── scripts/              # Optional - executable utilities
    └── helper.py
```

## Creating a Skill

### Step 1: Understand the Use Case

Ask clarifying questions:
- "What should this skill help you do?"
- "Can you give examples of how you'd use it?"
- "What would you say to trigger this skill?"

### Step 2: Create the Folder

```bash
# For personal skill
mkdir -p ~/.claude/skills/my-skill

# For project skill
mkdir -p .claude/skills/my-skill
```

### Step 3: Write SKILL.md

**Frontmatter rules:**
- `name`: lowercase, hyphens only, max 64 chars
- `description`: max 1024 chars, include BOTH what it does AND when to use it

### Step 4: Add References (if needed)

Keep SKILL.md under 500 lines. Move detailed content to `references/`:
- API docs, schemas → `references/`
- Large examples → `references/examples.md`
- Domain knowledge → `references/domain.md`

### Step 5: Add Scripts (if needed)

For repetitive or fragile operations, bundle scripts. Scripts are executed without loading into context - only output uses tokens.

### Step 6: Test

Restart Claude Code, then ask "What skills are available?" to verify it loaded.

## Core Principles

### Concise is Key
Claude is already smart. Only add what Claude doesn't know. Challenge every paragraph: "Does this justify its token cost?"

### Progressive Disclosure
- Metadata (name + description): Always loaded (~100 tokens)
- SKILL.md body: Loaded when triggered
- References: Loaded only when needed

### Degrees of Freedom
- **High freedom**: Text instructions when multiple approaches work
- **Low freedom**: Specific scripts when consistency is critical

## What NOT to Include

- README.md, CHANGELOG.md, INSTALLATION.md
- User-facing documentation
- Setup/testing procedures

Skills are for AI agents, not humans.

## Complete Example

A skill for generating commit messages:

```
commit-style/
├── SKILL.md
└── references/
    └── examples.md
```

**SKILL.md:**
```yaml
---
name: commit-style
description: Generate consistent commit messages following conventional commits. Use when committing code, creating PRs, or when user asks about commit message format.
---

# Commit Style

Generate commit messages using conventional commits format.

## Format

```
type(scope): brief description

Detailed explanation if needed
```

**Types:** feat, fix, docs, style, refactor, test, chore

## Quick Examples

- `feat(auth): add OAuth2 login flow`
- `fix(api): handle null response from payment service`
- `docs(readme): update installation instructions`

## Detailed Examples

For more examples by type, see [references/examples.md](references/examples.md)
```

**references/examples.md:**
```markdown
# Commit Examples by Type

## feat (new feature)
- `feat(cart): add quantity selector to cart items`
- `feat(search): implement fuzzy matching for product search`

## fix (bug fix)
- `fix(checkout): prevent double-submission of payment`
- `fix(auth): clear session on password change`

## refactor (code improvement)
- `refactor(api): extract validation into middleware`
- `refactor(components): convert class components to hooks`
```

## Workflow Patterns

For detailed patterns, see [references/patterns.md](references/patterns.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ainergiz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
