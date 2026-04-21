---
name: hooks-manager
description: Branch skill for building and improving hooks. Use when creating new hooks, adapting marketplace hooks, validating hook structure, writing hook scripts, or improving existing hooks. Triggers: 'create hook', 'improve hook', 'validate hook', 'fix hook', 'PreToolUse', 'PostToolUse', 'Stop hook', 'hook script', 'adapt hook', 'prompt hook', 'command hook'. Use when this capability is needed.
metadata:
  author: jarvis-projects
---

# Hooks Manager - Branch of JARVIS-05

Build and improve hooks following the hooks-management policy.

## Policy Source

**Primary policy**: JARVIS-05 → `.claude/skills/hooks-management/SKILL.md`

This branch **executes** the policy defined by JARVIS-05. Always sync with Primary before major operations.

## Quick Decision Tree

```text
Task Received
    │
    ├── Create new hook? ────────────────> Workflow 1: Build
    │   └── What type?
    │       ├── Context-aware logic ───────> Prompt hook (Recommended)
    │       ├── Validate/block ────────────> Command hook with exit 2
    │       ├── Log/audit ─────────────────> Command hook with exit 0
    │       └── File/external access ──────> Command hook
    │
    ├── Adapt marketplace hook? ─────────> Workflow 3: Adapt
    │
    ├── Fix existing hook? ──────────────> Workflow 2: Improve
    │
    └── Validate hook? ──────────────────> Validation Checklist
```

## Hook Types

### Prompt-Based Hooks (Recommended)

Use LLM-driven decision making for context-aware validation:

```json
{
  "type": "prompt",
  "prompt": "Evaluate if this tool use is appropriate: $TOOL_INPUT",
  "timeout": 30
}
```

**Supported events:** Stop, SubagentStop, UserPromptSubmit, PreToolUse

**Benefits:**
- Context-aware decisions based on natural language reasoning
- Flexible evaluation logic without bash scripting
- Better edge case handling
- Easier to maintain and extend

**Access input variables:**
- `$TOOL_INPUT` - Full tool input JSON
- `$TOOL_RESULT` - Tool result (PostToolUse)
- `$USER_PROMPT` - User's prompt text
- `$TRANSCRIPT_PATH` - Path to session transcript

### Command Hooks

Execute bash commands for deterministic checks:

```json
{
  "type": "command",
  "command": "bash ${CLAUDE_PLUGIN_ROOT}/scripts/validate.sh",
  "timeout": 60
}
```

**Use for:**
- Fast deterministic validations
- File system operations
- External tool integrations
- Performance-critical checks

| Type | Implementation | Use When | Output |
|------|----------------|----------|--------|
| prompt | Inline text | Complex logic, context-aware | LLM decision |
| command | Bash script | File access, validation, external tools | stdout/stderr + exit code |

## Hook Configuration Formats

### Plugin hooks.json Format

**For plugin hooks** in `hooks/hooks.json`, use wrapper format:

```json
{
  "description": "Brief explanation of hooks (optional)",
  "hooks": {
    "PreToolUse": [...],
    "Stop": [...],
    "SessionStart": [...]
  }
}
```

**Key points:**
- `description` field is optional
- `hooks` field is **required wrapper** containing actual hook events
- This is the **plugin-specific format**

### Settings Format (Direct)

**For user settings** in `.claude/settings.json`, use direct format:

```json
{
  "PreToolUse": [...],
  "Stop": [...],
  "SessionStart": [...]
}
```

**Key points:**
- No wrapper - events directly at top level
- No description field
- This is the **settings format**

**Important:** Plugin hooks.json requires the `{"hooks": {...}}` wrapper. Settings.json does not.

## Hook Events

| Event | When | Use For | Supports Prompt? |
|-------|------|---------|------------------|
| PreToolUse | Before tool executes | Validate, block, modify | ✓ |
| PostToolUse | After tool completes | Log, react, chain actions | ✗ |
| Stop | Before session ends | Completeness check | ✓ |
| SubagentStop | Before subagent stops | Task validation | ✓ |
| UserPromptSubmit | Before prompt sent | Add context, validate | ✓ |
| SessionStart | Session begins | Initialize environment | ✗ |
| SessionEnd | Session ends | Cleanup, logging | ✗ |
| PreCompact | Before context compaction | Preserve critical info | ✗ |
| Notification | On notifications | Alert routing | ✗ |

### PreToolUse

Execute before any tool runs. Use to approve, deny, or modify tool calls.

**Prompt-based example:**
```json
{
  "PreToolUse": [
    {
      "matcher": "Write|Edit",
      "hooks": [
        {
          "type": "prompt",
          "prompt": "Validate file write safety. Check: system paths, credentials, path traversal, sensitive content. Return 'approve' or 'deny'."
        }
      ]
    }
  ]
}
```

**Output format for PreToolUse:**
```json
{
  "hookSpecificOutput": {
    "permissionDecision": "allow|deny|ask",
    "updatedInput": {"field": "modified_value"}
  },
  "systemMessage": "Explanation for Claude"
}
```

### Stop

Execute when main agent considers stopping. Use to validate completeness.

**Example:**
```json
{
  "Stop": [
    {
      "matcher": "*",
      "hooks": [
        {
          "type": "prompt",
          "prompt": "Verify task completion: tests run, build succeeded, questions answered. Return 'approve' to stop or 'block' with reason to continue."
        }
      ]
    }
  ]
}
```

**Decision output:**
```json
{
  "decision": "approve|block",
  "reason": "Explanation",
  "systemMessage": "Additional context"
}
```

### SessionStart

Execute when Claude Code session begins. Use to load context and set environment.

**Example:**
```json
{
  "SessionStart": [
    {
      "matcher": "*",
      "hooks": [
        {
          "type": "command",
          "command": "bash ${CLAUDE_PLUGIN_ROOT}/scripts/load-context.sh"
        }
      ]
    }
  ]
}
```

**Special capability:** Persist environment variables using `$CLAUDE_ENV_FILE`:
```bash
#!/bin/bash
cd "$CLAUDE_PROJECT_DIR" || exit 1

# Detect project type and persist
if [ -f "package.json" ]; then
  echo "export PROJECT_TYPE=nodejs" >> "$CLAUDE_ENV_FILE"
elif [ -f "Cargo.toml" ]; then
  echo "export PROJECT_TYPE=rust" >> "$CLAUDE_ENV_FILE"
fi
```

## Hook Output Format

### Standard Output (All Hooks)

```json
{
  "continue": true,
  "suppressOutput": false,
  "systemMessage": "Message for Claude",
  "hookSpecificOutput": {
    "permissionDecision": "allow|deny|ask",
    "updatedInput": {"field": "modified_value"}
  }
}
```

- `continue`: If false, halt processing (default true)
- `suppressOutput`: Hide output from transcript (default false)
- `systemMessage`: Message shown to Claude
- `hookSpecificOutput`: Event-specific data (PreToolUse only)

### Exit Codes (Command Hooks)

| Exit Code | Meaning | Effect |
|-----------|---------|--------|
| 0 | Success | stdout shown in transcript |
| 2 | Block | stderr fed to Claude, action blocked |
| Other | Error | Logged, continues execution |

## Hook Input Format

All hooks receive JSON via stdin with common fields:

```json
{
  "session_id": "abc123",
  "transcript_path": "/path/to/transcript.txt",
  "cwd": "/current/working/dir",
  "permission_mode": "ask|allow",
  "hook_event_name": "PreToolUse"
}
```

**Event-specific fields:**

- **PreToolUse/PostToolUse:** `tool_name`, `tool_input`, `tool_result`
- **UserPromptSubmit:** `user_prompt`
- **Stop/SubagentStop:** `reason`

## Environment Variables

Available in all command hooks:

| Variable | Description | Available |
|----------|-------------|-----------|
| `$CLAUDE_PROJECT_DIR` | Project root path | Always |
| `$CLAUDE_PLUGIN_ROOT` | Plugin directory (use for portable paths) | Always |
| `$CLAUDE_ENV_FILE` | File to persist env vars | SessionStart only |
| `$CLAUDE_CODE_REMOTE` | Set if running in remote context | When remote |

**Always use ${CLAUDE_PLUGIN_ROOT} in hook commands for portability:**

```json
{
  "type": "command",
  "command": "bash ${CLAUDE_PLUGIN_ROOT}/scripts/validate.sh"
}
```

## Matchers

### Tool Name Matching

**Exact match:**
```json
"matcher": "Write"
```

**Multiple tools:**
```json
"matcher": "Read|Write|Edit"
```

**Wildcard (all tools):**
```json
"matcher": "*"
```

**Regex patterns:**
```json
"matcher": "mcp__.*__delete.*"
```

**Common patterns:**
```json
// All MCP tools
"matcher": "mcp__.*"

// Specific plugin's MCP tools
"matcher": "mcp__plugin_asana_.*"

// All file operations
"matcher": "Read|Write|Edit"

// Bash commands only
"matcher": "Bash"
```

**Note:** Matchers are case-sensitive.

## Workflow 1: Build New Hook

### Step 1: Define Hook Purpose

Answer these questions:

- What event should trigger this hook?
- What action should happen?
- Does it need context-aware reasoning? → Prompt hook
- Does it need file access or external tools? → Command hook
- Should it validate/block, log, or inject context?

### Step 2: Choose Hook Type

```text
Need context-aware logic? ───Yes──> prompt hook (Recommended)
Need file access?          ───Yes──> command hook
Need external tools?       ───Yes──> command hook
Need fast deterministic?   ───Yes──> command hook
Just add text?             ───Yes──> prompt hook
```

### Step 3: Write Hook Configuration

**Prompt Hook (Recommended for most cases):**

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "prompt",
            "prompt": "File path: $TOOL_INPUT.file_path. Verify: 1) Not in /etc or system directories 2) Not .env or credentials 3) Path doesn't contain '..' traversal. Return 'approve' or 'deny'.",
            "timeout": 30
          }
        ]
      }
    ]
  }
}
```

**Command Hook:**

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Write",
        "hooks": [
          {
            "type": "command",
            "command": "${CLAUDE_PLUGIN_ROOT}/hooks/scripts/validate-write.sh",
            "timeout": 10
          }
        ]
      }
    ]
  }
}
```

### Step 4: Write Hook Script (Command Hooks)

**Script Template:**

```bash
#!/bin/bash
# Hook: [Name]
# Event: [PreToolUse/PostToolUse/etc]
# Purpose: [What this hook does]

set -euo pipefail

# Read JSON input from stdin
input=$(cat)

# Parse input using jq with null safety
tool_name=$(echo "$input" | jq -r '.tool_name // empty')
file_path=$(echo "$input" | jq -r '.tool_input.file_path // empty')
session_id=$(echo "$input" | jq -r '.session_id // empty')

# Validate input exists
if [[ -z "$file_path" ]]; then
    echo "Warning: No file path provided" >&2
    exit 0  # Don't block on missing input
fi

# Validation logic
if [[ "$file_path" == *.env* ]]; then
    # Block: output to stderr, exit 2
    echo "BLOCKED: Cannot write to .env files" >&2
    exit 2
fi

# Success: output to stdout, exit 0
echo "Validated: $file_path"
exit 0
```

**Cross-Platform Wrapper** (for Windows compatibility):

```bash
#!/bin/bash
# Polyglot wrapper - works on Windows (Git Bash) and Unix

# Detect platform
if [[ "$OSTYPE" == "msys" ]] || [[ "$OSTYPE" == "cygwin" ]]; then
    # Windows path handling
    file_path=$(echo "$file_path" | sed 's|\\|/|g')
fi

# Rest of script...
```

### Step 5: Configure Timeout

```json
{
  "timeout": 10
}
```

Recommended timeouts:

| Hook Type | Use Case | Timeout |
|-----------|----------|---------|
| Prompt | Default | 30s |
| Command | Simple validation | 5-10s |
| Command | File operations | 10-30s |
| Command | External API calls | 30-60s |

### Step 6: Validate

Run full validation checklist.

## Workflow 2: Improve Existing Hook

### Step 1: Analyze Current State

```bash
# Check hook configuration
cat .claude/settings.json | jq '.hooks'

# Or for plugin hooks
cat hooks/hooks.json

# Check script exists and is executable
ls -la hooks/scripts/
file hooks/scripts/*.sh
```

### Step 2: Gap Analysis

| Component | Check | Common Issues |
|-----------|-------|---------------|
| Configuration | Valid JSON? | Missing timeout, wrong event name |
| Format | Plugin vs Settings? | Using wrong wrapper |
| Script path | Exists? Executable? | Wrong path, not chmod +x |
| Exit codes | Correct meaning? | Using exit 1 instead of exit 2 |
| Input parsing | jq correct? | Missing `// empty` for null safety |
| Error handling | Edge cases? | Script fails on unexpected input |
| Timeout | Appropriate? | Too short or missing |

### Step 3: Apply Fixes

**Adding timeout:**

```json
{
  "type": "command",
  "command": "script.sh",
  "timeout": 30
}
```

**Adding error handling to script:**

```bash
# Safe jq parsing with defaults
file_path=$(echo "$input" | jq -r '.tool_input.file_path // empty')
if [[ -z "$file_path" ]]; then
    echo "Warning: No file path provided" >&2
    exit 0  # Don't block on missing input
fi
```

**Fixing exit codes:**

```bash
# Wrong: exit 1 (error, continues)
# Right: exit 2 (block action)
exit 2
```

**Switching to prompt-based (recommended):**

```json
{
  "type": "prompt",
  "prompt": "Validate this file write operation. File: $TOOL_INPUT.file_path. Check for system paths, credentials, and path traversal. Return 'approve' or 'deny'."
}
```

### Step 4: Test Hook

1. Run Claude Code with `--debug` flag
2. Trigger the event manually
3. Check stdout/stderr output
4. Verify exit code behavior
5. Test edge cases (empty input, long paths, special chars)
6. Use `/hooks` command to review loaded hooks

### Step 5: Document Changes

Update comments in script with date and changes.

## Workflow 3: Adapt Marketplace Hook

When taking a hook from wshobson-agents, obra-superpowers, or similar:

### Step 1: Read Original Hook

```bash
# Check configuration
cat marketplace-plugin/hooks/hooks.json

# Check scripts
ls marketplace-plugin/hooks/scripts/
cat marketplace-plugin/hooks/scripts/*.sh
```

### Step 2: Identify JARVIS Fit

| Original Purpose | JARVIS Application |
|------------------|-------------------|
| Code validation | Adapt for JARVIS coding standards |
| File protection | Adapt for category-specific paths |
| Logging | Add BigQuery logging integration |
| Context injection | Add JARVIS-specific context |
| Stop validation | Add JARVIS completion criteria |

### Step 3: Adapt Configuration

**Original (may be command-based):**

```json
{
  "PreToolUse": [
    {
      "matcher": "Write",
      "hooks": [
        {
          "type": "command",
          "command": "validate.sh"
        }
      ]
    }
  ]
}
```

**Adapted for JARVIS (prefer prompt-based):**

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Write",
        "hooks": [
          {
            "type": "prompt",
            "prompt": "JARVIS file write validation. Path: $TOOL_INPUT.file_path. Check: 1) Not modifying Primary Skills without policy sync 2) Not overwriting CLAUDE.md identity 3) Path is within category bounds. Return 'approve' or 'deny'.",
            "timeout": 30
          }
        ]
      }
    ]
  }
}
```

### Step 4: Adapt Script (if keeping command hook)

**Add JARVIS-specific validation:**

```bash
#!/bin/bash
# Adapted from [source] for JARVIS ecosystem

input=$(cat)
file_path=$(echo "$input" | jq -r '.tool_input.file_path // empty')

# Original validation
if [[ "$file_path" == *.env* ]]; then
    echo "BLOCKED: Cannot write to .env files" >&2
    exit 2
fi

# JARVIS-specific: Protect Primary Skills
if [[ "$file_path" == *"/.claude/skills/"*"/SKILL.md" ]]; then
    echo "WARNING: Modifying Primary Skill - ensure policy compliance" >&2
    # Don't block, just warn
fi

# JARVIS-specific: Protect CLAUDE.md
if [[ "$file_path" == *"/CLAUDE.md" ]]; then
    echo "INFO: Updating category identity file"
fi

exit 0
```

### Step 5: Validate Adaptation

Run full validation checklist.

## Advanced Patterns

### Pattern 1: Multi-Stage Validation

Combine command and prompt hooks for layered validation:

```json
{
  "PreToolUse": [
    {
      "matcher": "Bash",
      "hooks": [
        {
          "type": "command",
          "command": "bash ${CLAUDE_PLUGIN_ROOT}/scripts/quick-check.sh",
          "timeout": 5
        },
        {
          "type": "prompt",
          "prompt": "Deep analysis of bash command: $TOOL_INPUT",
          "timeout": 15
        }
      ]
    }
  ]
}
```

All hooks run **in parallel** - design for independence.

### Pattern 2: Temporarily Active Hooks

Create hooks that activate conditionally:

```bash
#!/bin/bash
FLAG_FILE="$CLAUDE_PROJECT_DIR/.enable-strict-validation"

if [ ! -f "$FLAG_FILE" ]; then
  exit 0  # Skip when disabled
fi

# Flag present, run validation
input=$(cat)
# ... validation logic ...
```

### Pattern 3: Configuration-Driven Hooks

```bash
#!/bin/bash
CONFIG_FILE="$CLAUDE_PROJECT_DIR/.claude/plugin-config.json"

if [ -f "$CONFIG_FILE" ]; then
  strict_mode=$(jq -r '.strictMode // false' "$CONFIG_FILE")
  if [ "$strict_mode" != "true" ]; then
    exit 0
  fi
fi

# Strict mode enabled, run validation
```

### Pattern 4: Caching Validation Results

```bash
#!/bin/bash
input=$(cat)
file_path=$(echo "$input" | jq -r '.tool_input.file_path')
cache_key=$(echo -n "$file_path" | md5sum | cut -d' ' -f1)
cache_file="/tmp/hook-cache-$cache_key"

# Check cache (5 minute TTL)
if [ -f "$cache_file" ]; then
  cache_age=$(($(date +%s) - $(stat -c%Y "$cache_file" 2>/dev/null || stat -f%m "$cache_file")))
  if [ "$cache_age" -lt 300 ]; then
    cat "$cache_file"
    exit 0
  fi
fi

# Perform validation
result='{"decision": "approve"}'
echo "$result" > "$cache_file"
echo "$result"
```

### Pattern 5: Cross-Event Workflows

**SessionStart - Initialize tracking:**
```bash
#!/bin/bash
echo "0" > /tmp/test-count-$$
```

**PostToolUse - Track events:**
```bash
#!/bin/bash
input=$(cat)
tool_name=$(echo "$input" | jq -r '.tool_name')

if [[ "$tool_name" == "Bash" ]]; then
  command=$(echo "$input" | jq -r '.tool_result')
  if [[ "$command" == *"test"* ]]; then
    count=$(cat /tmp/test-count-$$ 2>/dev/null || echo "0")
    echo $((count + 1)) > /tmp/test-count-$$
  fi
fi
```

**Stop - Verify based on tracking:**
```bash
#!/bin/bash
test_count=$(cat /tmp/test-count-$$ 2>/dev/null || echo "0")

if [ "$test_count" -eq 0 ]; then
  echo '{"decision": "block", "reason": "No tests were run"}' >&2
  exit 2
fi
```

## Common Hook Patterns

### Validate File Writes

```bash
#!/bin/bash
input=$(cat)
file_path=$(echo "$input" | jq -r '.tool_input.file_path // empty')

# Block system directories
if [[ "$file_path" == /etc/* ]] || [[ "$file_path" == /sys/* ]]; then
    echo "BLOCKED: Cannot write to system directories" >&2
    exit 2
fi

# Block secrets files
if [[ "$file_path" == *.env* ]] || [[ "$file_path" == *credentials* ]]; then
    echo "BLOCKED: Cannot write to secrets files" >&2
    exit 2
fi

exit 0
```

### Log Operations to File

```bash
#!/bin/bash
input=$(cat)
timestamp=$(date -Iseconds)
tool_name=$(echo "$input" | jq -r '.tool_name // "unknown"')
session_id=$(echo "$input" | jq -r '.session_id // "unknown"')

echo "$timestamp | $session_id | $tool_name" >> /var/log/claude-ops.log
exit 0
```

### MCP Tool Validation

```bash
#!/bin/bash
input=$(cat)
tool_name=$(echo "$input" | jq -r '.tool_name // empty')

# Only validate MCP tools
if [[ "$tool_name" != mcp__* ]]; then
    exit 0
fi

# Check if MCP server is in allowed list
mcp_server=$(echo "$tool_name" | cut -d'_' -f3)
allowed_servers="vault supabase-common bigquery"

if [[ ! " $allowed_servers " =~ " $mcp_server " ]]; then
    echo "BLOCKED: MCP server '$mcp_server' not in allowed list" >&2
    exit 2
fi

exit 0
```

### Session Start Context (Prompt)

```json
{
  "SessionStart": [
    {
      "matcher": "*",
      "hooks": [
        {
          "type": "prompt",
          "prompt": "Session started. Remember:\n- Read CLAUDE.md first\n- Follow Primary Skill policies\n- Use improvement cycle every ~6 sessions"
        }
      ]
    }
  ]
}
```

### Verify Before Stop (Prompt - Recommended)

```json
{
  "Stop": [
    {
      "matcher": "*",
      "hooks": [
        {
          "type": "prompt",
          "prompt": "Review transcript. Verify: 1) Tests run after code changes 2) Build succeeded 3) All questions answered 4) No unfinished work. Return 'approve' only if complete."
        }
      ]
    }
  ]
}
```

## Hook Lifecycle

### Hooks Load at Session Start

**Important:** Hooks are loaded when Claude Code session starts. Changes to hook configuration require restarting Claude Code.

**Cannot hot-swap hooks:**
- Editing `hooks/hooks.json` won't affect current session
- Adding new hook scripts won't be recognized
- Changing hook commands/prompts won't update
- Must restart Claude Code: exit and run `claude` again

**To test hook changes:**
1. Edit hook configuration or scripts
2. Exit Claude Code session
3. Restart: `claude` or `cc`
4. New hook configuration loads
5. Test hooks with `claude --debug`

### Hook Validation at Startup

Hooks are validated when Claude Code starts:
- Invalid JSON in hooks.json causes loading failure
- Missing scripts cause warnings
- Syntax errors reported in debug mode

Use `/hooks` command to review loaded hooks in current session.

## Debugging Hooks

### Enable Debug Mode

```bash
claude --debug
```

Look for hook registration, execution logs, input/output JSON, and timing information.

### Test Hook Scripts Directly

```bash
echo '{"tool_name": "Write", "tool_input": {"file_path": "/test"}}' | \
  bash ${CLAUDE_PLUGIN_ROOT}/scripts/validate.sh

echo "Exit code: $?"
```

### Validate JSON Output

```bash
output=$(./your-hook.sh < test-input.json)
echo "$output" | jq .
```

### Review Loaded Hooks

Use `/hooks` command in Claude Code to see which hooks are active.

## Validation Checklist

### Configuration

- [ ] Hook defined correctly (settings.json or hooks/hooks.json)
- [ ] Correct format used (plugin wrapper vs settings direct)
- [ ] Event name is valid (PreToolUse, PostToolUse, Stop, etc.)
- [ ] Type is `command` or `prompt`
- [ ] Timeout specified (30s for prompt, 10-60s for command)

### Prompt Hooks

- [ ] Prompt text is clear and specific
- [ ] Uses appropriate input variables ($TOOL_INPUT, etc.)
- [ ] Specifies expected output format (approve/deny, etc.)
- [ ] No sensitive information in prompt
- [ ] Timeout set (default 30s)

### Command Hooks

- [ ] Script file exists at specified path
- [ ] Script is executable (`chmod +x`)
- [ ] Script has shebang (`#!/bin/bash`)
- [ ] Uses `${CLAUDE_PLUGIN_ROOT}` for portability
- [ ] Script reads input from stdin
- [ ] Script uses jq with `// empty` for null safety
- [ ] Exit 0 for success (stdout shown)
- [ ] Exit 2 for block (stderr fed to Claude)
- [ ] Error handling for edge cases
- [ ] All variables quoted (`"$var"` not `$var`)

### Matchers (if used)

- [ ] Matcher pattern is valid regex
- [ ] Matcher targets correct tools
- [ ] Case-sensitivity considered

### Testing

- [ ] Tested with `claude --debug`
- [ ] Tested with sample inputs
- [ ] Edge cases covered
- [ ] Timeout behavior verified

## Common Issues & Fixes

| Issue | Diagnosis | Fix |
|-------|-----------|-----|
| Hook not triggering | Event name wrong | Check spelling: PreToolUse not preToolUse |
| Script not found | Path wrong | Use `${CLAUDE_PLUGIN_ROOT}` for portability |
| No blocking effect | Wrong exit code | Use exit 2, not exit 1 |
| JSON parse error | jq syntax wrong | Add `// empty` for null safety |
| Timeout errors | Script too slow | Increase timeout or optimize script |
| Windows fails | Path separators | Use polyglot wrapper, convert \\ to / |
| Hook not loading | Wrong format | Plugin needs `{"hooks": {...}}` wrapper |
| Changes not applied | Session not restarted | Exit and restart Claude Code |

## Security Best Practices

**DO:**
- ✅ Use prompt-based hooks for complex logic
- ✅ Use ${CLAUDE_PLUGIN_ROOT} for portability
- ✅ Validate all inputs in command hooks
- ✅ Quote all bash variables (`"$var"`)
- ✅ Set appropriate timeouts
- ✅ Return structured JSON output
- ✅ Use `set -euo pipefail` in scripts

**DON'T:**
- ❌ Use hardcoded paths
- ❌ Trust user input without validation
- ❌ Create long-running hooks
- ❌ Rely on hook execution order (they run in parallel)
- ❌ Modify global state unpredictably
- ❌ Log sensitive information
- ❌ Use unquoted variables in bash

## When to Use This Skill

- User asks to create a new hook
- User asks to adapt a marketplace hook
- User asks to validate hook configuration
- User asks to debug hook behavior
- User asks about prompt vs command hooks
- DEV-Manager detects hook issues during improvement cycle
- Regular improvement cycle (~6 sessions)

## Sync Protocol

Before executing any workflow:

1. Read JARVIS-05's hooks-management SKILL.md
2. Check for policy updates
3. Apply current policy, not cached knowledge

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jarvis-projects) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
