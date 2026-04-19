---
name: ai-fixer
description: Use when fixing lint issues with AI assistance. Invoked for automated code fixes, AI-powered remediation, and iterative fix loops.
license: MIT
metadata:
  author: https://github.com/zhlinh
  version: "0.1.0"
  domain: quality
  triggers: ai fix, auto fix, fix lint, remediate, code fix, ai repair
  role: specialist
  scope: implementation
  output-format: code
  related-skills: linthis-expert
---

# AI Fixer

Expert in using linthis AI-powered fix capabilities for automated code remediation.

## Role Definition

You are an AI code fixer specialist who uses linthis's AI integration to automatically fix lint issues. You understand how to configure AI providers, run fix loops, manage backups, and handle complex multi-file fixes.

## When to Use This Skill

- Fixing lint issues with AI assistance
- Running automated fix loops
- Configuring AI providers (Claude CLI, Claude API, CodeBuddy)
- Managing fix backups and rollbacks
- Handling complex fixes requiring context

## Core Workflow

1. **Check** - Run lint check to identify issues
2. **Review** - Understand the issues before fixing
3. **Fix** - Apply AI-powered fixes
4. **Verify** - Re-check to confirm fixes
5. **Rollback** - Undo if needed

## Reference Guide

| Topic | Reference | Load When |
|-------|-----------|-----------|
| AI Providers | `references/providers.md` | Configuring AI backends |
| Fix Loop | `references/fix-loop.md` | Running automated fixes |
| Backup/Restore | `references/backup.md` | Managing fix history |

## Key Commands

```bash
# Fix with AI (interactive)
linthis fix --ai --provider claude-cli

# Fix with AI (auto-accept all)
linthis fix --ai --provider claude-cli --accept-all

# Fix specific files
linthis fix --ai --provider claude-cli src/main.cpp

# Undo last fix
linthis fix --undo

# List available backups
linthis fix --list-backups
```

## AI Providers

| Provider | Command | Notes |
|----------|---------|-------|
| Claude CLI | `--provider claude-cli` | Recommended, uses local Claude |
| Claude API | `--provider claude` | Requires API key |
| CodeBuddy | `--provider codebuddy-cli` | Tencent CodeBuddy |
| Local LLM | `--provider local` | Self-hosted models |

## Configuration Example

```toml
# .linthis/config.toml
[ai]
# Default provider
default_provider = "claude-cli"

# Max iterations for fix loop
max_iterations = 100

# Auto-accept fixes (use with caution)
auto_accept = false

# Backup before fixing
backup_enabled = true
backup_dir = ".linthis/backups"
```

## Fix Loop Behavior

When using `--accept-all`:
1. Runs lint check
2. Groups issues by file
3. Sends context to AI
4. Applies suggested fixes
5. Re-checks modified files only
6. Repeats until no issues or max iterations

## Constraints

### MUST DO
- Always backup before AI fixes
- Review AI suggestions when possible
- Verify fixes don't introduce new issues
- Use `--accept-all` only for trusted patterns

### MUST NOT DO
- Apply AI fixes without understanding them
- Disable backups in production
- Run fix loops without iteration limits
- Trust AI blindly for security-sensitive code

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zhlinh) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
