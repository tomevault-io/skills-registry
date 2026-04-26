---
name: claude-hooks
description: Claude Code hooks configuration specialist. Use when creating hooks for Use when this capability is needed.
metadata:
  author: 89jobrien
---

# Claude Hooks Skill

Creates and configures hooks for Claude Code to automate workflows and extend functionality.

## What This Skill Does

- Creates PreToolUse validation hooks
- Sets up PostToolUse logging/cleanup
- Configures notification hooks
- Implements custom automation
- Documents hook patterns

## When to Use

- Tool execution validation
- Audit logging
- Custom notifications
- Workflow automation
- Security controls

## Reference Files

- `references/CLAUDE_HOOK.template.md` - Hook configuration examples and patterns

## Hook Events

| Event | Trigger | Use Case |
|-------|---------|----------|
| PreToolUse | Before tool executes | Validation, blocking |
| PostToolUse | After tool completes | Logging, cleanup |
| Notification | Claude sends notification | Alerts |
| Stop | Claude stops | Final reports |

## Configuration Location

Hooks are configured in `~/.claude/settings.json` under the `hooks` key.

## Best Practices

- Keep hooks fast (< 1 second)
- Handle errors gracefully
- Use specific matchers
- Test hooks independently
- Avoid verbose output

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/89jobrien) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
