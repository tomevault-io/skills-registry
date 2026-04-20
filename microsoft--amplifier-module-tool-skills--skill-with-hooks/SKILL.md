---
name: skill-with-hooks
description: Example skill demonstrating embedded hooks for validation and logging Use when this capability is needed.
metadata:
  author: microsoft
---

# Skill With Hooks Example

This is an example skill that demonstrates how to embed Claude Code-compatible hooks
directly in a skill's frontmatter.

## Purpose

When this skill is loaded, its hooks become active for the session:

1. **PreToolUse (Bash)**: Validates bash commands before execution
2. **PreToolUse (all tools)**: Logs all tool invocations
3. **PostToolUse (all tools)**: Logs tool results

## Hook Scripts

The hooks reference scripts in the `./hooks/` directory relative to this skill:

- `validate-bash.sh` - Checks bash commands for dangerous patterns
- `log-tool-use.sh` - Logs tool name and parameters
- `log-tool-result.sh` - Logs tool execution results

## Usage

Load this skill to activate its hooks:

```
load_skill(skill_name="skill-with-hooks")
```

The hooks will remain active until the session ends.

## Testing

This skill is used to validate the skill-scoped hooks integration between
`amplifier-module-tool-skills` and `amplifier-module-hooks-shell`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/microsoft) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
