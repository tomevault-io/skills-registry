---
name: spawn-session
description: Spawn a new Claude Code tmux session as a subagent worker Use when this capability is needed.
metadata:
  author: shsym
---

# Spawn Session

Create a new Claude Code tmux session with `ai-*` prefix to act as a subagent worker.

## Arguments

`$ARGUMENTS` format: `[type] [name-suffix] [-- initial prompt]`

- `type` - Session type: `worker` (default), `brainstorm`, `auditor`
- `name-suffix` - Optional custom suffix (auto-generated if omitted)
- `-- initial prompt` - Optional prompt to send after session starts

Examples:
- `/session-tools:spawn-session` → `ai-worker-001`
- `/session-tools:spawn-session worker api` → `ai-worker-api`
- `/session-tools:spawn-session brainstorm design` → `ai-brainstorm-design`
- `/session-tools:spawn-session worker task1 -- Implement the login feature` → spawns and sends prompt

## Instructions

1. **Parse arguments:**
   ```
   type = "worker" (default)
   suffix = auto-generated or provided
   prompt = text after "--" if present
   ```

2. **Generate session name:**

   If no suffix provided, find next available number:
   ```bash
   "$PLUGIN_DIR/bin/next-session-name" "<type>"
   ```

   Returns: `ai-<type>-<number>` (e.g., `ai-worker-003`)

   If suffix provided: `ai-<type>-<suffix>`

3. **Validate session doesn't exist:**
   ```bash
   tmux has-session -t "<session_name>" 2>/dev/null && echo "exists"
   ```

   If exists, error:
   ```
   Session '<session_name>' already exists. Choose a different name.
   ```

4. **Get working directory:**

   Use current working directory for the new session:
   ```bash
   pwd
   ```

5. **Spawn the session:**
   ```bash
   "$PLUGIN_DIR/bin/spawn-session" "<session_name>" "<working_dir>" ["<initial_prompt>"]
   ```

   The script:
   - Creates tmux session in detached mode
   - Starts `claude` in the session
   - Optionally sends initial prompt

6. **Verify session started:**
   ```bash
   sleep 2
   "$PLUGIN_DIR/bin/detect-state" "<session_name>"
   ```

7. **Output result:**
   ```
   Spawned session: <session_name>
   Working directory: <working_dir>
   Status: <state>

   To monitor: /session-tools:summarize-session <session_name>
   To attach:  tmux attach -t <session_name>
   ```

## Session Types

| Type | Prefix | Purpose |
|------|--------|---------|
| `worker` | `ai-worker-*` | Task execution |
| `brainstorm` | `ai-brainstorm-*` | Ideation and exploration |
| `auditor` | `ai-auditor-*` | Code review and verification |

## Example Usage

**Spawn default worker:**
```
/session-tools:spawn-session

Spawned session: ai-worker-001
Working directory: /Users/you/project
Status: idle - At prompt

To monitor: /session-tools:summarize-session ai-worker-001
To attach:  tmux attach -t ai-worker-001
```

**Spawn with task:**
```
/session-tools:spawn-session worker api -- Implement the REST API endpoints for user authentication

Spawned session: ai-worker-api
Working directory: /Users/you/project
Status: working - Processing prompt

To monitor: /session-tools:summarize-session ai-worker-api
To attach:  tmux attach -t ai-worker-api
```

**Spawn brainstorm session:**
```
/session-tools:spawn-session brainstorm arch -- What are the best approaches for implementing caching in this codebase?

Spawned session: ai-brainstorm-arch
Working directory: /Users/you/project
Status: working - Analyzing
```

## Workflow: Using as Subagents

1. **Spawn workers for parallel tasks:**
   ```
   /session-tools:spawn-session worker auth -- Implement authentication
   /session-tools:spawn-session worker api -- Implement API routes
   /session-tools:spawn-session worker tests -- Write integration tests
   ```

2. **Monitor progress:**
   ```
   /session-tools:list-sessions
   ```

3. **Check specific worker:**
   ```
   /session-tools:summarize-session ai-worker-auth
   ```

4. **Handle blockers:**
   ```
   /session-tools:approve-action ai-worker-auth approve
   /session-tools:unstick-session ai-worker-api auto
   ```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shsym) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
