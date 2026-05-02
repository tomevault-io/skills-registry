---
name: codex-cli
description: Execute tasks using OpenAI's Codex CLI. Use when asked to run codex, use OpenAI for code review, or delegate coding tasks to Codex. Supports non-interactive execution, code review, and task automation. Use when this capability is needed.
metadata:
  author: gexijin
---

# OpenAI Codex CLI Skill

This skill provides integration with OpenAI's Codex CLI for code review and task execution.

## Quick Reference

### Code Review

Review uncommitted changes:
```bash
codex review --uncommitted
```

Review against a branch:
```bash
codex review --base main
```

Review a specific commit:
```bash
codex review --commit <sha>
```

Review with custom instructions:
```bash
codex review "Focus on security vulnerabilities"
```

### Task Execution

Run a task non-interactively:
```bash
codex exec "<prompt>"
```

With full auto mode (sandboxed automatic execution):
```bash
codex exec --full-auto "<prompt>"
```

With specific model:
```bash
codex exec -m o3 "<prompt>"
```

## Available Commands

| Command | Description |
|---------|-------------|
| `codex review` | Run code review non-interactively |
| `codex exec` | Run tasks non-interactively |
| `codex` | Interactive mode |
| `codex apply` | Apply latest diff from Codex agent |
| `codex resume` | Resume previous session |

## Review Command Options

| Option | Description |
|--------|-------------|
| `--uncommitted` | Review staged, unstaged, and untracked changes |
| `--base <branch>` | Review changes against base branch |
| `--commit <sha>` | Review changes from a specific commit |
| `--title <title>` | Optional commit title for review summary |
| `-c model=<model>` | Use specific model |

## Exec Command Options

| Option | Description |
|--------|-------------|
| `-m, --model <model>` | Model to use (e.g., o3, gpt-4) |
| `-s, --sandbox <mode>` | Sandbox: read-only, workspace-write, danger-full-access |
| `--full-auto` | Low-friction sandboxed automatic execution |
| `-C, --cd <dir>` | Working directory |
| `-i, --image <file>` | Attach image(s) to prompt |
| `-o, --output-last-message <file>` | Write final response to file |
| `--json` | Output events as JSONL |

## Common Use Cases

### 1. Quick Code Review

Review current changes before committing:
```bash
codex review --uncommitted
```

### 2. PR Review

Review all changes in a feature branch:
```bash
codex review --base main --title "Feature: Add user authentication"
```

### 3. Focused Review

Review with specific focus:
```bash
codex review --uncommitted "Focus on:
1. Security vulnerabilities
2. Error handling
3. Performance issues"
```

### 4. Automated Task

Run a coding task:
```bash
codex exec "Add input validation to all API endpoints in src/api/"
```

### 5. Code Explanation

Get explanation of complex code:
```bash
codex exec "Explain the algorithm in src/utils/ranking.R"
```

### 6. Bug Investigation

Investigate test failures:
```bash
codex exec "Investigate why tests in tests/unit/ are failing"
```

## Sandbox Modes

| Mode | Permissions |
|------|-------------|
| `read-only` | Can read files, no writes |
| `workspace-write` | Can write to workspace (default with --full-auto) |
| `danger-full-access` | Full system access (use with caution) |

## Integration with Claude Code

When using this skill:

1. **For code review**: Use `/codex-review` command or run `codex review` directly
2. **For tasks**: Use `/codex` command or run `codex exec` directly
3. **For complex workflows**: Combine Codex output with Claude Code analysis

### Example Workflow

1. Use Codex for initial review:
   ```bash
   codex review --uncommitted > review.txt
   ```

2. Have Claude Code analyze and summarize:
   - Read the review output
   - Identify critical issues
   - Suggest fixes

## Best Practices

1. **Use `--full-auto` for routine tasks** - Speeds up execution with safe defaults
2. **Specify `--base` for PR reviews** - Ensures complete diff is reviewed
3. **Add context to review prompts** - Better reviews with specific focus areas
4. **Check sandbox mode** - Use appropriate permissions for the task
5. **Save output for complex reviews** - Use `-o` flag for later analysis

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gexijin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
