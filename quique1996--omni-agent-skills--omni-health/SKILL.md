---
name: omni-health
description: Verify your development environment is properly configured. Checks git, Ollama, databases, MCP servers, and more. Use when this capability is needed.
metadata:
  author: quique1996
---

# Omni-Health

Development environment health check. Run at the start of a session to verify everything is working.

## Usage

When the user invokes `/omni-health`, run the health check.

### Commands

**Full health check** (human-readable):
```bash
python3 ~/.claude/skills/omni-health/scripts/session-health-check.py
```

**JSON output** (for scripts/automation):
```bash
python3 ~/.claude/skills/omni-health/scripts/session-health-check.py --json
```

## What It Checks

### Git Status
- Current branch
- Uncommitted changes count
- Recent commits (last 3)

### Ollama
- Is Ollama running?
- What models are available?
- Are recommended models installed?

### PostgreSQL (optional)
- Connection test
- Basic query execution

### tmux Sessions
- Active background sessions
- Claude-related sessions

### MCP Servers
- Configured servers count
- Missing recommended servers

### Pre-commit Hooks
- Is pre-commit installed?
- Hook configuration

### Tech Debt
- TODO count in codebase
- FIXME count (higher priority)

## Understanding the Output

```
Overall Status: OK | WARNING | ERROR

OK      - Everything working
WARNING - Some non-critical issues
ERROR   - Critical issues need attention
```

### Example Output

```
============================================================
  OMNI-AGENT SESSION HEALTH CHECK
  2026-01-27T10:30:00
============================================================

Overall Status: WARNING

Git: main (2 uncommitted)
   - abc1234 Add new feature
   - def5678 Fix bug

Ollama: 4 models (qwen3:14b, deepseek-r1:14b, ...)
   - qwen2.5-coder:14b available (recommended)

PostgreSQL: successful

tmux: 1 active session: claude-background

MCP Servers: 6 configured
   - Missing: playwright

Tech Debt: 5 TODOs, 1 FIXME

Warnings:
   - git: 2 uncommitted changes
   - tech_debt: 1 FIXME found
============================================================
```

## Exit Codes

- `0` - All checks passed (OK)
- `1` - Warnings present
- `2` - Errors present

## Customization

Edit the script to:
- Add/remove checks
- Change database connection details
- Modify recommended MCP servers list
- Adjust tech debt thresholds

## Requirements

- Python 3.10+
- Git
- curl (for Ollama check)

### Optional
- Ollama
- PostgreSQL
- tmux
- pre-commit

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/quique1996) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
