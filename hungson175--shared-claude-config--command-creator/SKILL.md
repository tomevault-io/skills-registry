---
name: command-creator
description: Create custom slash commands for Claude Code. Use this skill when the user asks to "create a command", "add a slash command", "make a custom command", "write a command for...", or needs help with command frontmatter, arguments ($ARGUMENTS, $1, $2), file references (@syntax), bash execution in commands, or organizing commands in project/personal/plugin locations. Use when this capability is needed.
metadata:
  author: hungson175
---

# Command Creator

Create custom slash commands as Markdown files that Claude executes when invoked.

## Command Locations

| Type | Location | Scope | Label |
|------|----------|-------|-------|
| **Project** | `.claude/commands/` | Current project, shared with team | `(project)` |
| **Personal** | `~/.claude/commands/` | All projects, user-specific | `(user)` |
| **Plugin** | `plugin-name/commands/` | When plugin installed | `(plugin-name)` |

## File Structure

```
.claude/commands/
├── review.md           # /review
├── deploy.md           # /deploy
└── frontend/
    └── component.md    # /frontend:component (namespaced)
```

## YAML Frontmatter Fields

```yaml
---
description: Brief description shown in /help (under 60 chars)
allowed-tools: Read, Write, Bash(git:*)
model: sonnet  # sonnet | opus | haiku
argument-hint: [pr-number] [priority]
disable-model-invocation: false
---
```

| Field | Purpose | Default |
|-------|---------|---------|
| `description` | Shown in `/help` | First line of prompt |
| `allowed-tools` | Tools command can use | Inherits from conversation |
| `model` | Model for execution | Inherits from conversation |
| `argument-hint` | Document expected args | None |
| `disable-model-invocation` | Prevent programmatic invocation | false |

## Dynamic Arguments

**$ARGUMENTS** - Capture all arguments as single string:
```markdown
Fix issue #$ARGUMENTS following our coding standards.
# /fix-issue 123 → Fix issue #123 following our coding standards.
```

**$1, $2, $3...** - Positional arguments:
```markdown
Review PR #$1 with priority $2. Assign to $3.
# /review-pr 123 high alice → Review PR #123 with priority high. Assign to alice.
```

## File References (@syntax)

```markdown
Review @$1 for code quality.           # Dynamic: /review-file src/api.ts
Compare @src/old.js with @src/new.js   # Static file references
Review @package.json and @tsconfig.json
```

## Bash Execution

Include dynamic context with `!` backtick syntax:

```markdown
Files changed: !`git diff --name-only`
Current branch: !`git branch --show-current`
```

**Requires** `allowed-tools: Bash(git:*)` or similar in frontmatter.

## Command Templates

### Basic Review Command
```markdown
---
description: Review code for issues
allowed-tools: Read, Bash(git:*)
---

Review the following changes for:
- Code quality and style
- Potential bugs
- Test coverage needs
```

### With Arguments
```markdown
---
description: Fix issue by number
argument-hint: [issue-number]
---

Fix issue #$ARGUMENTS following project standards and best practices.
```

### Multi-Argument Workflow
```markdown
---
description: Deploy to environment
argument-hint: [environment] [version]
---

Deploy version $2 to $1 environment.

Pre-deploy checks:
1. Verify environment is valid (dev|staging|prod)
2. Confirm version exists
3. Run deployment
```

### File-Based Command
```markdown
---
description: Document a file
argument-hint: [file-path]
---

Generate documentation for @$1 including:
- Function descriptions
- Parameters and return values
- Usage examples
```

## Best Practices

1. **Single responsibility** - One command, one task
2. **Clear descriptions** - Self-explanatory in `/help`
3. **Document arguments** - Always use `argument-hint`
4. **Limit tool scope** - Use `Bash(git:*)` not `Bash(*)`
5. **Verb-noun naming** - `review-pr`, `fix-issue`, `deploy-app`

## Common Patterns

See `references/patterns.md` for:
- Review patterns (code, PR, security)
- Testing patterns
- Documentation generation
- Multi-step workflows
- Validation patterns

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hungson175) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
