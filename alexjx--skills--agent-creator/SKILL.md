---
name: agent-creator
description: Create Claude Code subagents. Use when user wants to create a subagent, specialized agent, or custom AI assistant for Claude Code. Use when this capability is needed.
metadata:
  author: alexjx
---

# Agent Creator

Creates Claude Code subagents - specialized AI assistants defined as markdown files.

## What is a Subagent?

A subagent is a markdown file with YAML frontmatter that defines a specialized AI assistant. Claude Code delegates tasks to subagents based on their description.

## File Format

```markdown
---
name: agent-name
description: When to use this agent. Include trigger words.
tools: Read, Grep, Glob
model: sonnet
---

System prompt goes here. Define the agent's role, process, and output format.
```

## Configuration Fields

| Field | Required | Description |
|-------|----------|-------------|
| `name` | Yes | Lowercase letters and hyphens only |
| `description` | Yes | When Claude should use this agent |
| `tools` | No | Comma-separated list. Omit to inherit all tools |
| `model` | No | `haiku`, `sonnet`, `opus`, or `inherit` |

## Available Tools

`Read`, `Write`, `Edit`, `Bash`, `Glob`, `Grep`, `WebFetch`, `WebSearch`, plus any MCP tools.

## File Locations

| Location | Scope |
|----------|-------|
| `.claude/agents/` | Current project only |
| `~/.claude/agents/` | All projects for this user |

## Creating a Subagent

1. Ask user for:
   - Agent name
   - Purpose and when to trigger
   - Required tools (minimal set)
   - Model choice

2. Create the markdown file with:
   - Clear, specific description with trigger words
   - Focused system prompt
   - Minimal tool permissions

3. Save to the location user specifies

## Best Practices

- **Focused purpose**: One clear responsibility per agent
- **Specific triggers**: Include action words in description ("Use when...", "Proactively...")
- **Minimal tools**: Only grant necessary tools
- **Clear output format**: Define expected response structure in prompt

## Example: Code Reviewer

```markdown
---
name: code-reviewer
description: Reviews code for quality and security. Use after code changes.
tools: Read, Grep, Glob, Bash
model: sonnet
---

You are a code reviewer. When invoked:

1. Run git diff to see changes
2. Review for quality, security, maintainability
3. Provide feedback by priority: Critical > Warnings > Suggestions
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alexjx) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
