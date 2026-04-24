---
name: opencode-skills
description: |- Use when this capability is needed.
metadata:
  author: justinlevinedotme
---

<overview>

Create loadable knowledge modules that enhance agent capabilities.

A skill is a markdown file that provides specialized knowledge, workflows, or tool integrations. Skills are loaded on-demand via the `skill` tool when needed.

</overview>

<rules>

## Skill Fundamentals

### Location

| Scope | Path |
|-------|------|
| Project | `.opencode/skill/<name>/SKILL.md` |
| Global | `~/.config/opencode/skill/<name>/SKILL.md` |

**CRITICAL**: The folder name MUST match the `name` in frontmatter.

### Directory Structure

```
.opencode/skill/my-skill/
├── SKILL.md           # REQUIRED - Main skill definition
├── scripts/           # Optional - Executable scripts
│   └── validate.sh
├── references/        # Optional - Reference documents
│   └── schema.md
└── assets/            # Optional - Images, data files
    └── diagram.png
```

### Naming Rules

| Rule | Valid | Invalid |
|------|-------|---------|
| Lowercase | `my-skill` | `My-Skill` |
| Alphanumeric + hyphen | `api-client` | `api_client` |
| Match folder name | `foo/SKILL.md` with `name: foo` | Mismatch |

</rules>

<guidelines>

## Frontmatter Schema

```yaml
---
name: my-skill-name          # REQUIRED, MUST match folder name
description: |-              # REQUIRED, 1-1024 characters
  [Capability summary]. Use for [use cases].
  
  Use proactively when [trigger contexts].
  
  Examples:
  - user: "query" → action
  - user: "another query" → action
---
```

### Description Pattern

The description MUST have three parts:

1. **Capability statement**: What the skill does (1-2 sentences)
2. **Trigger contexts**: "Use proactively when [conditions]"
3. **Examples**: `user: "query" → action` format

```yaml
description: |-
  Generate comprehensive API documentation from source code. Use for documenting REST endpoints, GraphQL schemas, and SDK methods.
  
  Use proactively when user says "document API", "generate docs", "API reference", or asks about endpoint documentation.
  
  Examples:
  - user: "Document this FastAPI app" → analyze routes, generate OpenAPI-style docs
  - user: "Create SDK documentation" → extract methods, parameters, return types
```

</guidelines>

<examples>

## Skill Content Structure

### Recommended Sections

```markdown
---
name: example-skill
description: |-
  [Description as above]
---

# Skill Title

Brief overview of what this skill enables.

## Core Concepts

Fundamental knowledge the agent needs.

## Workflow

Step-by-step process:

1. **Understand**: Gather requirements
2. **Analyze**: Assess current state
3. **Implement**: Make changes
4. **Verify**: Confirm correctness

## Patterns

### Pattern Name

\`\`\`language
// Code example
\`\`\`

## Anti-Patterns

- **Problem**: Description → **Solution**: Fix

## Validation

How to verify the skill's output is correct.
```

### Length Guidelines

| Guideline | Recommendation |
|-----------|----------------|
| SKILL.md total length | SHOULD be under 500 lines |
| Complex documentation | SHOULD use `references/` dir |
| Code examples | SHOULD use `scripts/` dir |
| Progressive disclosure | Core in SKILL.md, details in references |

</examples>

<rules>

## Permission Configuration

Control which skills agents can load:

```yaml
# In agent frontmatter or opencode.json
permission:
  skill:
    "*": "deny"              # Deny all by default
    "security-*": "allow"    # Allow security-prefixed
    "my-skill": "allow"      # Allow specific skill
```

### Pattern Matching

| Pattern | Matches |
|---------|---------|
| `"*"` | All skills |
| `"prefix-*"` | Skills starting with `prefix-` |
| `"exact-name"` | Only that exact skill |

</rules>

<guidelines>

## References Directory

For detailed documentation that would bloat SKILL.md:

```
.opencode/skill/my-skill/
├── SKILL.md
└── references/
    ├── schema.md          # JSON schema documentation
    ├── examples.md        # Extended examples
    └── troubleshooting.md # Common issues
```

Reference in SKILL.md:
```markdown
See `references/schema.md` for the complete configuration schema.
```

## Scripts Directory

For executable utilities:

```
.opencode/skill/my-skill/
├── SKILL.md
└── scripts/
    ├── validate.sh        # Validation script
    └── generate.py        # Generator script
```

Reference in SKILL.md:
```markdown
Run validation:
\`\`\`bash
bash .opencode/skill/my-skill/scripts/validate.sh
\`\`\`
```

## Skill Discovery

Skills are discovered at:
1. Project: `.opencode/skill/*/SKILL.md`
2. Global: `~/.config/opencode/skill/*/SKILL.md`

Project skills take precedence over global skills with the same name.

</guidelines>

<constraints>

## Best Practices

### MUST

- Use the description pattern (capability + triggers + examples)
- Keep SKILL.md focused and under 500 lines
- Use `references/` for detailed documentation
- Test skill loading with `skill my-skill-name`
- Include validation steps

### MUST NOT

- Create overly broad skills (prefer focused, composable skills)
- Duplicate content that exists in other skills
- Include secrets or credentials in skill files
- Forget to match folder name with frontmatter name

</constraints>

<examples>

## Validation

After creating a skill:

```bash
opencode run "test"
```

Then test loading:
```
Load the my-skill-name skill and explain what it does.
```

</examples>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/justinlevinedotme) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
