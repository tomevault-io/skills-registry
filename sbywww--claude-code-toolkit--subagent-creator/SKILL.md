---
name: subagent-creator
description: Guide for creating effective subagents. This skill should be used when users want to create a new subagent (or update an existing subagent) -- specialized AI assistants that run in isolated context windows with custom system prompts, tool restrictions, and independent permissions. Use when users ask to create, design, or configure a Claude Code subagent/agent. Use when this capability is needed.
metadata:
  author: sbywww
---

# Subagent Creator

This skill provides guidance for creating effective subagents.

## About Subagents

Subagents are specialized AI assistants that run in isolated context windows with their own system prompts, tool sets, and permission settings. They are defined as single `.md` files with YAML frontmatter for configuration and a markdown body for the system prompt.

### Subagents vs Skills

| Aspect | Subagent | Skill |
|--------|----------|-------|
| Format | Single `.md` file | Directory with `SKILL.md` + resources |
| Purpose | Isolated execution context | Knowledge/workflow extension |
| Invocation | Delegated to by parent agent via `Task` tool | Triggered by matching description |
| Context | Independent context window | Shared context with parent |
| Configuration | Tools, model, permissions, hooks | Name and description only |

### File Format

```
---
name: my-subagent              # Required: hyphen-case identifier
description: "..."             # Required: when to delegate to this subagent
tools: Read, Glob, Grep        # Optional: allowed tools (allowlist)
disallowedTools: Bash          # Optional: denied tools (denylist)
model: sonnet                  # Optional: haiku | sonnet | opus | inherit
permissionMode: default        # Optional: permission behavior
skills:                        # Optional: skills to preload
  - skill-name
hooks:                         # Optional: tool execution hooks
  PreToolUse: [...]
---

System prompt content goes here.
```

### File Locations

- **Project scope**: `.claude/agents/<name>.md` — available in the current project
- **User scope**: `~/.claude/agents/<name>.md` — available across all projects

## Core Principles

### Focused Responsibility

Each subagent should do one thing well. A code reviewer reviews code. A test runner runs tests. Combining multiple responsibilities into one subagent dilutes the system prompt and makes the `description` field ambiguous for delegation.

### Description Drives Delegation

The `description` field is how the parent agent decides whether to delegate a task to this subagent. It must be specific and actionable — describe the exact task types and trigger conditions. A vague description leads to incorrect or missed delegation.

### Minimal Tool Surface

Grant only the tools the subagent actually needs. A read-only reviewer needs `Read, Glob, Grep`, not `Write` or `Bash`. Fewer tools mean fewer failure modes and safer automated execution.

### System Prompt Conciseness

The system prompt occupies the subagent's context window. Keep it focused on the task at hand. Move detailed reference material to skills (via the `skills` field) or let the subagent read files on demand rather than embedding everything in the prompt.

## Subagent Creation Process

Follow these steps in order. Skip only when there is a clear reason.

### Step 1: Understand the Purpose

Before creating a subagent, gather concrete examples of how it will be used:

- What specific tasks will be delegated to it?
- What does the parent agent say when delegating?
- What output format is expected?
- How frequently will it be invoked?

Example questions to ask:
- "What would a typical task look like for this subagent?"
- "Should it modify files, or only read and report?"
- "Does it need shell access? If so, for what commands?"

### Step 2: Plan the Configuration

Based on the use cases, determine:

1. **Tools**: What's the minimum tool set needed? See `references/tool-selection.md` for common combinations.
2. **Model**: Does the task need `opus`-level reasoning, or can `haiku` handle it?
3. **Permission mode**: How much autonomy should the subagent have?
4. **Hooks**: Are there commands or actions that need guardrails?
5. **Skills**: Does the subagent benefit from preloaded skill knowledge?

Consult `references/tool-selection.md` for detailed guidance on each configuration option.

For established design patterns that match common use cases, see `references/subagent-patterns.md`.

### Step 3: Initialize the File

Run the initialization script to generate a template file:

```bash
scripts/init_subagent.py <name> --scope <project|user>
```

Or specify a custom directory:

```bash
scripts/init_subagent.py <name> --path <directory>
```

The script creates a `.md` file with the frontmatter template and TODO placeholders. All optional fields are included as YAML comments for reference.

### Step 4: Edit the Subagent

#### Frontmatter

Fill in the required fields and uncomment/configure optional fields as needed:

- **`name`**: Hyphen-case identifier (`a-z`, `0-9`, `-`), max 64 characters
- **`description`**: Specific explanation of what the subagent does and when to delegate to it. This is the primary delegation trigger — make it precise.
- **`tools`**: Comma-separated list of allowed tools, or omit for all tools
- **`model`**: Choose based on task complexity (default: `inherit`)
- **`permissionMode`**: Start with `default`, promote as trust is established

#### System Prompt

Write a focused system prompt that tells the subagent:

1. **What it is**: Role definition in one sentence
2. **What to do**: Numbered steps for the standard workflow
3. **What to avoid**: Explicit boundaries (if needed)
4. **Output format**: How to structure the response (if specific format is needed)

Keep it concise. The best system prompts are under 200 words.

### Step 5: Validate

Run the validation script to check the file against the spec:

```bash
scripts/validate_subagent.py <path-to-subagent.md>
```

The validator checks:
- Frontmatter structure and required fields
- Name format and length
- Description constraints
- Tool names, model values, permission modes
- Hook event names
- System prompt body presence
- Remaining TODO placeholders (warning)

Fix any errors and re-run until validation passes.

### Step 6: Iterate

Test the subagent with real tasks and refine:

1. Invoke the subagent on representative tasks
2. Observe where it struggles or produces unexpected results
3. Adjust the system prompt, tools, or model as needed
4. Re-validate after changes

Common iteration adjustments:
- **Too slow**: Switch to `haiku` for simple tasks
- **Missing context**: Add relevant skills or expand the system prompt
- **Unsafe operations**: Restrict tools or add `PreToolUse` hooks
- **Poor delegation**: Rewrite the `description` with more specific trigger conditions

## Design Patterns

For detailed patterns with configuration examples and system prompt skeletons, see `references/subagent-patterns.md`. Key patterns include:

1. **Read-Only Reviewer** — Safe analysis without modification risk
2. **Focused Fixer** — Single-category problem diagnosis and resolution
3. **Cost-Optimized Explorer** — `haiku`-powered fast codebase navigation
4. **Validated Shell Access** — Hook-guarded command execution
5. **Skill-Augmented Specialist** — Domain expertise via preloaded skills

Also review the anti-patterns section to avoid common mistakes: multi-purpose agents, vague descriptions, excessive tool access, and unguarded shell access.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sbywww) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
