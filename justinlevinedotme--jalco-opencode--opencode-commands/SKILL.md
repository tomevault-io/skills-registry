---
name: opencode-commands
description: |- Use when this capability is needed.
metadata:
  author: justinlevinedotme
---

<overview>

Create user-accessible slash commands that streamline workflows.

</overview>

<rules>

## Command Fundamentals

### Location

| Scope | Path |
|-------|------|
| Project | `.opencode/command/<name>.md` |
| Global | `~/.config/opencode/command/<name>.md` |

Commands MAY also be defined inline in `opencode.json`:

```jsonc
{
  "command": {
    "my-command": {
      "template": "Do the thing with $ARGUMENTS",
      "description": "One-line description"
    }
  }
}
```

### Frontmatter Options

| Field | Type | Description |
|-------|------|-------------|
| `description` | string | REQUIRED. Brief description shown in command list |
| `agent` | string | Route to specific agent (e.g., `plan` for read-only research) |
| `model` | string | Override model for this command |
| `subtask` | boolean | If true, runs as background task via task tool |

### Template Placeholders

| Placeholder | Expansion |
|-------------|-----------|
| `$ARGUMENTS` | All arguments after the command |
| `$1`, `$2`... | Positional arguments (space-separated) |
| `` `!cmd` `` | Output of shell command (e.g., `` `!git status` ``) |
| `@filename` | Contents of file (e.g., `@src/main.ts`) |

</rules>

<examples>

## Command Patterns

### Simple Command

```markdown
---
description: Run all tests with coverage
---

Run tests with coverage reporting:
\`!npm run test:coverage\`
```

### Agent-Routed Command

```markdown
---
description: Research a topic without modifying files
agent: explore
---

Research the following topic thoroughly, gathering context from the codebase and web:

$ARGUMENTS
```

### Workflow Command with Arguments

```markdown
---
description: Create a new component with tests
---

Create a new React component named "$1" in the $2 directory.

Requirements:
1. Component file with TypeScript
2. Test file with basic render test
3. Index export

Use the existing patterns from @src/components/Button/index.tsx
```

### Background Task Command

```markdown
---
description: Deep research task (runs in background)
subtask: true
agent: explore
---

Perform comprehensive research on: $ARGUMENTS

Return findings in a structured report.
```

</examples>

<constraints>

## Best Practices

### MUST

- Keep descriptions under 80 characters
- Use `agent: explore` or `agent: plan` for read-only operations
- Use `subtask: true` for long-running operations
- Include example arguments in the description when helpful
- Use `@file` to inject context from existing code

### MUST NOT

- Create commands that duplicate existing tools
- Use commands for one-off tasks (just ask directly)
- Forget to test with `opencode run "test"` after changes
- Route to agents that don't exist

</constraints>

<guidelines>

## Command vs Direct Request

Commands are USER shortcuts. Use them when:
- You repeat the same request pattern frequently
- You want consistent agent/model routing
- You need to inject file context or shell output

For one-off tasks, just ask Claude directly.

</guidelines>

<examples>

## Validation

After creating a command:

```bash
opencode run "test"
```

Then test with:

```
/your-command test arguments
```

</examples>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/justinlevinedotme) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
