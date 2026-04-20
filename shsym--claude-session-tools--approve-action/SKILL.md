---
name: approve-action
description: Detect and handle permission prompts in a Claude Code tmux session Use when this capability is needed.
metadata:
  author: shsym
---

# Approve Action

Detect permission prompts and approve or deny them.

## Arguments

`$ARGUMENTS`: `<session-name> [approve|deny]`

## Instructions

1. **Parse arguments:**
   - Session name (required)
   - Action: `approve`, `deny`, or none (interactive)

2. **Capture session output:**
   ```bash
   "$PLUGIN_DIR/bin/capture-session" "<session_name>" 30
   ```

3. **Look for permission prompts:**
   - `Bash command:` followed by command
   - `Allow` ... `to` ...
   - `Grant access`
   - `Use skill "..."`
   - Permission dialog boxes

4. **If no permission prompt found:**
   ```
   No pending permission request in session '<session_name>'.
   ```

5. **If permission prompt found, show details:**
   ```
   ## Permission Request: <session_name>

   **Type:** <Bash/Edit/Skill/etc>
   **Details:** <what is being requested>

   Options:
   - approve: Allow this action
   - deny: Reject this action
   ```

6. **If action specified (approve/deny):**

   Send the appropriate response:
   ```bash
   # For approve:
   tmux send-keys -t "<session>" "y" && tmux send-keys -t "<session>" Enter

   # For deny:
   tmux send-keys -t "<session>" "n" && tmux send-keys -t "<session>" Enter
   ```

   Report:
   ```
   Sent '<action>' to session '<session_name>'.
   ```

## Example

**Interactive:**
```
/session-tools:approve-action ai-worker-001

## Permission Request: ai-worker-001

**Type:** Bash
**Details:** npm install express

Options:
- /session-tools:approve-action ai-worker-001 approve
- /session-tools:approve-action ai-worker-001 deny
```

**Direct:**
```
/session-tools:approve-action ai-worker-001 approve

Sent 'approve' to session 'ai-worker-001'.
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shsym) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
