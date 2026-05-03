---
name: python-hooks
description: Guide for writing Python hooks in Claude Code. Use when the user wants to create hooks, write hook scripts, automate Claude behavior, add custom permissions, auto-format code, log actions, or understand hook events like PreToolUse, PostToolUse, UserPromptSubmit, or Stop. Use when this capability is needed.
metadata:
  author: srulyrosenblat
---

# Python Hooks for Claude Code

This skill teaches how to write Python hooks that automate Claude Code behavior. Hooks are shell commands that execute at specific points in Claude's lifecycle, providing **deterministic control** over actions.

## When to Use Hooks

- **Auto-format code** after edits (prettier, black, etc.)
- **Block dangerous commands** before execution
- **Auto-approve safe operations** (skip permission prompts)
- **Add context** to prompts (current time, project info)
- **Log actions** for compliance or debugging
- **Custom notifications** when Claude needs input

---

## Hook Events Reference

| Event | When It Runs | Common Use Cases |
|-------|--------------|------------------|
| **PreToolUse** | Before tool executes | Validate commands, block files, auto-approve |
| **PostToolUse** | After tool completes | Format code, run linters, validate output |
| **UserPromptSubmit** | User submits prompt | Add context, block sensitive patterns |
| **PermissionRequest** | Permission dialog shown | Auto-allow/deny permissions |
| **Stop** | Claude finishes responding | Force continuation if incomplete |
| **SubagentStop** | Subagent finishes | Evaluate task completion |
| **SessionStart** | Session begins | Setup environment variables |
| **SessionEnd** | Session ends | Cleanup, logging |
| **Notification** | Claude sends notification | Custom desktop alerts |

---

## Python Hook Template

Every Python hook follows this structure:

```python
#!/usr/bin/env python3
import json
import sys

try:
    input_data = json.load(sys.stdin)
except json.JSONDecodeError as e:
    print(f"Error: Invalid JSON: {e}", file=sys.stderr)
    sys.exit(1)

# Access common fields
tool_name = input_data.get("tool_name", "")
tool_input = input_data.get("tool_input", {})
hook_event = input_data.get("hook_event_name", "")

# Your logic here...

sys.exit(0)  # Success
```

**Make it executable:**
```bash
chmod +x your_hook.py
```

---

## Exit Codes

| Code | Behavior | When to Use |
|------|----------|-------------|
| **0** | Success. JSON in stdout is parsed | Normal operation, approvals |
| **1** | Non-blocking error. stderr shown in verbose mode | Warnings that shouldn't stop Claude |
| **2** | Blocking error. stderr used as error message | Critical issues that must block the action |

**Important:** With exit code 2, only stderr is used—stdout JSON is ignored.

---

## JSON Input (stdin)

All hooks receive this JSON structure:

```json
{
  "session_id": "abc123",
  "transcript_path": "/path/to/transcript.jsonl",
  "cwd": "/current/working/dir",
  "hook_event_name": "PreToolUse",
  "tool_name": "Write",
  "tool_input": {
    "file_path": "/path/to/file.txt",
    "content": "file content"
  }
}
```

**Event-specific fields:**
- `tool_name`, `tool_input` — PreToolUse, PostToolUse, PermissionRequest
- `prompt` — UserPromptSubmit
- `source` — SessionStart (`"startup"` or `"resume"`)

---

## JSON Output (stdout)

For structured control, output JSON to stdout with exit code 0:

```python
output = {
    "decision": "block",  # or "approve", undefined
    "reason": "Explanation for user/Claude",
    "hookSpecificOutput": {
        "hookEventName": "PreToolUse",
        "permissionDecision": "allow"  # "allow", "deny", "ask"
    }
}
print(json.dumps(output))
sys.exit(0)
```

**Permission decisions (PreToolUse/PermissionRequest):**
- `"allow"` — Auto-approve, show reason to user
- `"deny"` — Block action, show reason to Claude
- `"ask"` — Prompt user for confirmation

---

## Configuration

Add hooks to `~/.claude/settings.json` or `.claude/settings.json`:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "python3 ~/.claude/hooks/validate_bash.py",
            "timeout": 30
          }
        ]
      }
    ],
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "\"$CLAUDE_PROJECT_DIR\"/.claude/hooks/format.py"
          }
        ]
      }
    ]
  }
}
```

**Matcher patterns:**
- Exact: `"Bash"`, `"Write"`, `"Edit"`
- Multiple: `"Edit|Write"` (regex OR)
- All MCP tools: `"mcp__.*"`
- Specific MCP: `"mcp__memory__create_entities"`

---

## Complete Examples

### Example 1: Validate Bash Commands (PreToolUse)

Block inefficient commands and suggest alternatives:

```python
#!/usr/bin/env python3
"""Block grep/find, suggest ripgrep alternatives."""
import json
import re
import sys

RULES = [
    (r"\bgrep\b(?!.*\|)", "Use 'rg' (ripgrep) instead of 'grep'"),
    (r"\bfind\s+\S+\s+-name\b", "Use 'rg --files' instead of 'find -name'"),
    (r"\bcat\s+\S+\s*\|\s*grep", "Use 'rg' directly on the file"),
]

try:
    data = json.load(sys.stdin)
except json.JSONDecodeError:
    sys.exit(1)

if data.get("tool_name") != "Bash":
    sys.exit(0)

command = data.get("tool_input", {}).get("command", "")
issues = [msg for pattern, msg in RULES if re.search(pattern, command)]

if issues:
    for msg in issues:
        print(f"• {msg}", file=sys.stderr)
    sys.exit(2)  # Block the command

sys.exit(0)
```

---

### Example 2: Auto-Approve Safe Files (PreToolUse)

Skip permission prompts for documentation:

```python
#!/usr/bin/env python3
"""Auto-approve reading documentation files."""
import json
import sys

try:
    data = json.load(sys.stdin)
except json.JSONDecodeError:
    sys.exit(1)

tool_name = data.get("tool_name", "")
file_path = data.get("tool_input", {}).get("file_path", "")

SAFE_EXTENSIONS = (".md", ".mdx", ".txt", ".json", ".yaml", ".yml")

if tool_name == "Read" and file_path.endswith(SAFE_EXTENSIONS):
    output = {
        "hookSpecificOutput": {
            "hookEventName": "PreToolUse",
            "permissionDecision": "allow",
            "permissionDecisionReason": "Documentation file auto-approved"
        }
    }
    print(json.dumps(output))

sys.exit(0)
```

---

### Example 3: Block Sensitive Files (PreToolUse)

Prevent edits to critical files:

```python
#!/usr/bin/env python3
"""Block modifications to sensitive files."""
import json
import sys

PROTECTED = [".env", "package-lock.json", "yarn.lock", ".git/", "secrets"]

try:
    data = json.load(sys.stdin)
except json.JSONDecodeError:
    sys.exit(1)

if data.get("tool_name") not in ["Edit", "Write"]:
    sys.exit(0)

file_path = data.get("tool_input", {}).get("file_path", "")

for pattern in PROTECTED:
    if pattern in file_path:
        print(f"Blocked: Cannot modify protected file ({pattern})", file=sys.stderr)
        sys.exit(2)

sys.exit(0)
```

---

### Example 4: Auto-Format After Edit (PostToolUse)

Run formatter on edited files:

```python
#!/usr/bin/env python3
"""Auto-format Python files after editing."""
import json
import subprocess
import sys

try:
    data = json.load(sys.stdin)
except json.JSONDecodeError:
    sys.exit(1)

file_path = data.get("tool_input", {}).get("file_path", "")

if not file_path.endswith(".py"):
    sys.exit(0)

try:
    result = subprocess.run(
        ["black", "--quiet", file_path],
        capture_output=True,
        text=True,
        timeout=30
    )
    if result.returncode == 0:
        print(f"Formatted: {file_path}")
except FileNotFoundError:
    print("Warning: black not installed", file=sys.stderr)
except subprocess.TimeoutExpired:
    print("Warning: formatting timed out", file=sys.stderr)

sys.exit(0)
```

---

### Example 5: Add Context to Prompts (UserPromptSubmit)

Inject useful context into every prompt:

```python
#!/usr/bin/env python3
"""Add current time and git branch to prompts."""
import json
import subprocess
import sys
from datetime import datetime

try:
    data = json.load(sys.stdin)
except json.JSONDecodeError:
    sys.exit(1)

# Get current time
now = datetime.now().strftime("%Y-%m-%d %H:%M")

# Get git branch
try:
    branch = subprocess.check_output(
        ["git", "branch", "--show-current"],
        text=True,
        stderr=subprocess.DEVNULL
    ).strip()
except Exception:
    branch = "unknown"

context = f"[Time: {now}] [Branch: {branch}]"
print(context)  # Simple stdout adds context

sys.exit(0)
```

---

### Example 6: Block Sensitive Prompts (UserPromptSubmit)

Prevent accidental secret exposure:

```python
#!/usr/bin/env python3
"""Block prompts containing potential secrets."""
import json
import re
import sys

SENSITIVE_PATTERNS = [
    (r"(?i)(password|secret|key|token)\s*[:=]\s*\S+", "Possible secret detected"),
    (r"(?i)api[_-]?key\s*[:=]", "API key pattern detected"),
    (r"-----BEGIN (RSA |EC |DSA )?PRIVATE KEY-----", "Private key detected"),
]

try:
    data = json.load(sys.stdin)
except json.JSONDecodeError:
    sys.exit(1)

prompt = data.get("prompt", "")

for pattern, message in SENSITIVE_PATTERNS:
    if re.search(pattern, prompt):
        output = {
            "decision": "block",
            "reason": f"Security: {message}. Please rephrase without sensitive data."
        }
        print(json.dumps(output))
        sys.exit(0)

sys.exit(0)
```

---

### Example 7: Environment Setup (SessionStart)

Initialize environment variables for the session:

```python
#!/usr/bin/env python3
"""Set up environment variables at session start."""
import json
import os
import sys

try:
    data = json.load(sys.stdin)
except json.JSONDecodeError:
    sys.exit(1)

# Persist environment variables if available
env_file = os.environ.get("CLAUDE_ENV_FILE")
if env_file:
    try:
        with open(env_file, "a") as f:
            f.write("export NODE_ENV=development\n")
            f.write("export DEBUG=true\n")
    except Exception as e:
        print(f"Warning: Could not write env: {e}", file=sys.stderr)

# Add context about session
source = data.get("source", "startup")
output = {
    "hookSpecificOutput": {
        "hookEventName": "SessionStart",
        "additionalContext": f"Session started ({source})"
    }
}
print(json.dumps(output))

sys.exit(0)
```

---

### Example 8: Log All Commands (PreToolUse)

Audit log for compliance:

```python
#!/usr/bin/env python3
"""Log all tool usage to a file."""
import json
import os
import sys
from datetime import datetime

LOG_FILE = os.path.expanduser("~/.claude/audit.log")

try:
    data = json.load(sys.stdin)
except json.JSONDecodeError:
    sys.exit(1)

tool_name = data.get("tool_name", "unknown")
tool_input = data.get("tool_input", {})
timestamp = datetime.now().isoformat()

log_entry = {
    "timestamp": timestamp,
    "tool": tool_name,
    "input": tool_input
}

try:
    with open(LOG_FILE, "a") as f:
        f.write(json.dumps(log_entry) + "\n")
except Exception:
    pass  # Don't fail on logging errors

sys.exit(0)
```

---

## Environment Variables

| Variable | Available In | Purpose |
|----------|--------------|---------|
| `CLAUDE_PROJECT_DIR` | All hooks | Absolute path to project root |
| `CLAUDE_CODE_REMOTE` | All hooks | `"true"` if web environment |
| `CLAUDE_ENV_FILE` | SessionStart only | File path for persisting env vars |

**Usage:**
```python
import os
project_dir = os.environ.get("CLAUDE_PROJECT_DIR", "")
script_path = os.path.join(project_dir, ".claude", "hooks", "my_hook.py")
```

---

## Best Practices

1. **Always validate JSON input** — Wrap `json.load()` in try/except
2. **Use absolute paths** — Reference scripts via `$CLAUDE_PROJECT_DIR`
3. **Handle missing fields** — Use `.get()` with defaults
4. **Set timeouts** — Prevent hanging with `timeout` in settings
5. **Fail gracefully** — Non-critical errors should exit 1, not 2
6. **Log for debugging** — Write to `~/.claude/hooks.log` during development
7. **Check tool name first** — Exit early if hook doesn't apply

---

## Security Considerations

**Hooks run with your credentials automatically!**

- Review all hook code before adding
- Never run untrusted hooks
- Sanitize file paths (check for `..`)
- Don't expose secrets in logs
- Use exit code 2 sparingly (blocks action completely)

---

## Troubleshooting

**Hook not running?**
- Check `/hooks` command to see registered hooks
- Verify file is executable: `chmod +x hook.py`
- Check matcher matches the tool name exactly

**Hook errors?**
- Run `claude --debug` for verbose output
- Test script manually: `echo '{"tool_name":"Bash"}' | python3 hook.py`
- Check stderr output for error messages

**JSON not parsing?**
- Ensure valid JSON syntax in settings.json
- Use spaces, not tabs for indentation
- Validate with `python3 -m json.tool settings.json`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/srulyrosenblat) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
