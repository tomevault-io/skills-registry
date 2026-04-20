---
name: summarize-session
description: Get a detailed summary of what's happening in a Claude Code tmux session Use when this capability is needed.
metadata:
  author: shsym
---

# Summarize Session

Capture and analyze a session's recent activity.

## Arguments

`$ARGUMENTS`: `<session-name>`

## Instructions

1. **Validate session exists:**
   ```bash
   tmux has-session -t "<session_name>" 2>/dev/null || echo "NOT_FOUND"
   ```

2. **Capture extended output (500 lines):**
   ```bash
   "$PLUGIN_DIR/bin/capture-session" "<session_name>" 500
   ```

3. **Analyze the output** to identify:
   - **Current state**: working, idle, stuck, waiting for input
   - **Recent tool calls**: Look for `⏺` markers (Read, Edit, Bash, etc.)
   - **Files being worked on**: Paths mentioned in tool calls
   - **Current task**: What is Claude working on?
   - **Errors/warnings**: Any error messages or failures
   - **Progress**: What has been accomplished?

4. **Output structured summary:**
   ```markdown
   # Session Summary: <session_name>

   ## Current State
   <working/idle/stuck/waiting>

   ## Current Task
   <description of what Claude is doing>

   ## Recent Activity
   - <tool call 1>
   - <tool call 2>
   - ...

   ## Files Touched
   - <file 1>
   - <file 2>

   ## Errors/Issues
   <any errors or None>

   ## Progress
   <summary of what's been done>
   ```

## Example

```
/session-tools:summarize-session ai-worker-001

# Session Summary: ai-worker-001

## Current State
working

## Current Task
Implementing user authentication API endpoints

## Recent Activity
- Read src/api/routes.ts
- Edit src/api/auth.ts (added login endpoint)
- Bash: npm test
- Read test output

## Files Touched
- src/api/routes.ts
- src/api/auth.ts
- src/api/middleware/auth.ts

## Errors/Issues
None

## Progress
- Created login endpoint
- Added JWT token generation
- Currently writing tests
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shsym) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
