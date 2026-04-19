---
name: creating-claude-skills
description: Expert guide for creating Claude Code skills with proper directory structure, YAML frontmatter, and auto-discovery. Use when creating skills, authoring SKILL.md files, writing skill descriptions, or converting existing guides to skills. Use when this capability is needed.
metadata:
  author: thegaltinator
---

# Creating Claude Code Skills

## Quick Start

```
my-skill/
├── SKILL.md           # Required - YAML frontmatter + instructions
├── reference.md       # Optional - loaded on demand
└── scripts/
    └── helper.py      # Optional - executed, not loaded into context
```

## SKILL.md Template

```yaml
---
name: my-skill-name           # Required: lowercase, hyphens, max 64 chars
description: What + when      # Required: max 1024 chars, triggers discovery
allowed-tools: Read, Bash     # Optional: auto-approve tools
model: claude-sonnet-4        # Optional: specific model
context: fork                 # Optional: isolated sub-agent
---

# Skill Title

## Instructions
[Clear guidance]

## Examples
[Concrete examples]
```

## Frontmatter Fields

| Field | Required | Description |
|-------|----------|-------------|
| `name` | Yes | Lowercase, hyphens only, matches directory |
| `description` | Yes | What + when to use (triggers auto-discovery) |
| `allowed-tools` | No | Tools Claude can use without asking |
| `model` | No | Specific model for this skill |
| `context` | No | `fork` = isolated sub-agent context |
| `user-invocable` | No | `false` = hide from slash menu |

## Skill Locations

| Location | Path | Scope |
|----------|------|-------|
| Personal | `~/.claude/skills/` | Your account everywhere |
| Project | `.claude/skills/` | Anyone in this repo |

## Writing Effective Descriptions

The description is **critical** - it determines when Claude auto-discovers the skill.

```yaml
# GOOD - Specific with trigger keywords
description: Extract text and tables from PDF files, fill forms, merge documents. Use when working with PDF files or when the user mentions PDFs, forms, or document extraction.

# BAD - Too vague
description: Helps with documents
```

**Rules:**
- Use third person ("Processes files" not "I can help you")
- Include trigger keywords
- Specify when to use it
- Max 1024 characters

## Best Practices

### 1. Keep Under 500 Lines
Use progressive disclosure - put details in separate reference files.

### 2. Be Concise
Claude is smart. Only include what it doesn't already know.

### 3. Scripts = Zero Context Cost
When Claude runs `python scripts/analyze.py`, only output enters context (not the code).

### 4. References One Level Deep
```
GOOD: SKILL.md → reference.md
BAD:  SKILL.md → a.md → b.md → c.md
```

## Instruction Patterns

### Workflows with Steps
```markdown
- [ ] Step 1: Analyze (`python scripts/analyze.py`)
- [ ] Step 2: Validate (`python scripts/validate.py`)
- [ ] Step 3: Execute (`python scripts/execute.py`)
```

### Feedback Loops
```markdown
1. Make edits
2. **Validate**: `python scripts/validate.py`
3. If fails → fix → validate again
4. Only proceed when passing
```

### Examples Pattern
```markdown
**Example 1:**
Input: Added user auth with JWT
Output: `feat(auth): implement JWT-based authentication`
```

### Conditional Workflows
```markdown
1. Determine type:
   - **Creating new?** → Follow "Creation workflow"
   - **Editing existing?** → Follow "Editing workflow"
```

## Common Anti-Patterns

| Anti-Pattern | Solution |
|--------------|----------|
| Vague descriptions | Include trigger keywords |
| Too many options | Provide default + escape hatch |
| Deeply nested references | Keep one level deep |
| Windows paths (`\`) | Always use forward slashes |
| Punting errors to Claude | Handle in scripts |

## Checklist

- [ ] Directory structure: `skill-name/SKILL.md`
- [ ] YAML frontmatter with `name` and `description`
- [ ] Description has trigger keywords
- [ ] Under 500 lines
- [ ] Third-person descriptions
- [ ] References one level deep
- [ ] Forward slashes in paths

## Testing

1. Run `/skills` to verify skill appears
2. Ask a question matching the description
3. Verify skill activation prompt appears
4. Test with different models (Haiku, Sonnet, Opus)

## Sources

- [Agent Skills - Claude Code Docs](https://code.claude.com/docs/en/skills)
- [Skill Authoring Best Practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thegaltinator) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
