---
name: add-hook
description: Add event hooks to a Claude Code plugin. Use when user wants to run code automatically on events like tool use, session start, etc. Use when this capability is needed.
metadata:
  author: nrempel
---

# Add Hook

You are adding event hooks to a Claude Code plugin. Hooks run automatically in response to Claude Code events. Follow these steps:

## 1. Gather Information

Ask the user for:
- **Plugin path** (which plugin to add to, or current directory)
- **Event type** (which event triggers the hook?)
- **Hook type** (command, prompt, or agent?)
- **What should happen** (script to run or prompt to evaluate)
- **Matcher** (optional: filter to specific tools)

## 2. Available Events

| Event | Triggered When |
|-------|----------------|
| `PreToolUse` | Before Claude uses any tool |
| `PostToolUse` | After successful tool use |
| `PostToolUseFailure` | After a tool fails |
| `UserPromptSubmit` | User submits a prompt |
| `SessionStart` | Session begins |
| `SessionEnd` | Session ends |
| `SubagentStart` | A subagent is spawned |
| `SubagentStop` | A subagent completes |
| `PreCompact` | Before context compaction |
| `Notification` | Claude sends a notification |
| `Stop` | Claude attempts to stop |

## 3. Create or Update hooks.json

Location: `<plugin>/hooks/hooks.json`

```json
{
  "hooks": {
    "<EventName>": [
      {
        "matcher": "<ToolName|Pattern>",
        "hooks": [
          {
            "type": "command",
            "command": "${CLAUDE_PLUGIN_ROOT}/scripts/<script>.sh"
          }
        ]
      }
    ]
  }
}
```

## 4. Hook Types

### Command Hook
Runs a shell command:
```json
{
  "type": "command",
  "command": "${CLAUDE_PLUGIN_ROOT}/scripts/lint.sh"
}
```

### Prompt Hook
Evaluates with an LLM:
```json
{
  "type": "prompt",
  "prompt": "Check if this action is safe",
  "timeout": 5000
}
```

### Agent Hook
Runs agentic verification:
```json
{
  "type": "agent",
  "prompt": "Analyze this change for issues"
}
```

## 5. Matcher Patterns

- `"Write|Edit"` - Matches Write or Edit tools
- `"Read"` - Matches Read tool only
- `""` - Matches all (empty string)
- Tool names are case-sensitive

## 6. Exit Codes (for command hooks)

- `0` - Success, continue
- `1` - Failure, block the action
- `2` - Intervention, ask user

## 7. Environment Variables

Always use `${CLAUDE_PLUGIN_ROOT}` for paths in hooks - it resolves to the plugin's absolute path.

## 8. Example: Lint on Write

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "${CLAUDE_PLUGIN_ROOT}/scripts/lint.sh"
          }
        ]
      }
    ]
  }
}
```

After creating hooks.json, remind the user to:
1. Create any referenced scripts
2. Make scripts executable: `chmod +x scripts/*.sh`
3. Test the hook by triggering the event

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nrempel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
