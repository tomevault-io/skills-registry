---
name: permissions-guide
description: Reference for Claude Code permission configuration. Invoke when setting up permissions, debugging access issues, or understanding security implications. Use when this capability is needed.
metadata:
  author: reubenjohn
---

# Claude Code Permissions Guide

Understanding and configuring `.claude/settings.json` permissions for security-conscious Reeve deployments.

## Overview

Claude Code uses a permission system to control what actions Reeve can take autonomously. The `.claude/settings.json` file defines which tools and operations are pre-authorized vs. which require explicit user approval.

**Key principle:** Start restrictive, expand thoughtfully. Every permission you grant is a capability you're giving to an AI agent.

## Permission Syntax

```json
{
  "permissions": {
    "allow": [
      "ToolName(pattern)",
      "mcp__server-name__tool_name",
      "mcp__server-name"
    ]
  }
}
```

### Permission Types

| Pattern | Meaning | Example |
|---------|---------|---------|
| `ToolName` | Allow tool with no arguments | `Read` - read files with approval per-file |
| `ToolName(*)` | Allow tool with ANY arguments | `Read(*)` - read any file without asking |
| `ToolName(pattern)` | Allow tool matching pattern | `Bash(git *)` - allow git commands only |
| `mcp__server__tool` | Allow specific MCP tool | `mcp__pulse-queue__list_upcoming_pulses` |
| `mcp__server` | Allow ALL tools from MCP server | `mcp__pulse-queue` - all pulse operations |

## The Default Template Permissions

The template ships with minimal permissions:

```json
{
  "permissions": {
    "allow": [
      "mcp__telegram-notifier__send_notification",
      "mcp__pulse-queue__schedule_pulse",
      "mcp__pulse-queue__list_upcoming_pulses",
      "mcp__pulse-queue__cancel_pulse",
      "mcp__pulse-queue__reschedule_pulse"
    ]
  }
}
```

**Why these?**
- Notifications and pulse management are core to Reeve's proactive operation
- These don't read, modify, or delete your files
- Each pulse/notification is logged and auditable

## Permission Levels: A Security Framework

### Level 1: Minimal (Default)
```json
{
  "permissions": {
    "allow": [
      "mcp__telegram-notifier__send_notification",
      "mcp__pulse-queue__schedule_pulse",
      "mcp__pulse-queue__list_upcoming_pulses",
      "mcp__pulse-queue__cancel_pulse",
      "mcp__pulse-queue__reschedule_pulse"
    ]
  }
}
```
**Use when:** First setting up Reeve, want manual approval for file operations
**Reeve can:** Send notifications, schedule/manage pulses
**Reeve cannot:** Read/write files, run commands, search web (without asking)

### Level 2: Read-Only Desk Access
```json
{
  "permissions": {
    "allow": [
      "mcp__telegram-notifier__send_notification",
      "mcp__pulse-queue__schedule_pulse",
      "mcp__pulse-queue__list_upcoming_pulses",
      "mcp__pulse-queue__cancel_pulse",
      "mcp__pulse-queue__reschedule_pulse",
      "Read([YOUR_DESK_PATH]/*)"
    ]
  }
}
```
**Use when:** Want Reeve to read your Desk (Goals, Preferences, etc.) automatically
**Reeve can:** Read files in your Desk directory
**Reeve cannot:** Write files, read outside Desk, run commands

### Level 3: Full Desk Access (Read + Write)
```json
{
  "permissions": {
    "allow": [
      "mcp__telegram-notifier__send_notification",
      "mcp__pulse-queue",
      "Read([YOUR_DESK_PATH]/*)",
      "Write([YOUR_DESK_PATH]/*)",
      "Edit([YOUR_DESK_PATH]/*)"
    ]
  }
}
```
**Use when:** Want Reeve to manage your Desk autonomously (Diary entries, etc.)
**Security note:** Reeve can now modify your files. Changes are tracked in git.
**Reeve cannot:** Read/write outside Desk, run commands

### Level 4: Git Operations
```json
{
  "permissions": {
    "allow": [
      "mcp__telegram-notifier__send_notification",
      "mcp__pulse-queue",
      "Read([YOUR_DESK_PATH]/*)",
      "Write([YOUR_DESK_PATH]/*)",
      "Edit([YOUR_DESK_PATH]/*)",
      "Bash(git status*)",
      "Bash(git add*)",
      "Bash(git commit*)",
      "Bash(git log*)",
      "Bash(git diff*)"
    ]
  }
}
```
**Use when:** Want Reeve to commit changes to your Desk automatically
**Security note:** Explicitly allow only safe git commands (no push, no force)
**Reeve cannot:** Push to remote, run other bash commands

### Level 5: Autonomous Operation (High Trust)
```json
{
  "permissions": {
    "allow": [
      "Bash(*)",
      "Edit(*)",
      "Write(*)",
      "Read(*)",
      "mcp__pulse-queue",
      "mcp__telegram-notifier",
      "WebSearch",
      "WebFetch"
    ]
  }
}
```
**Use when:** Fully trust Reeve, want maximum autonomy
**WARNING:** This grants near-complete system access. Reeve can:
- Read/write ANY file on your system
- Execute ANY bash command
- Search the web
- Access any configured MCP server

## Security Implications by Permission

### File Operations

| Permission | Risk Level | Implication |
|------------|------------|-------------|
| `Read([PATH]/*)` | Low | Can read files in PATH only |
| `Read(*)` | Medium | Can read ANY file (including secrets, configs) |
| `Write([PATH]/*)` | Medium | Can create/modify files in PATH |
| `Write(*)` | High | Can overwrite ANY file (including configs, code) |
| `Edit(*)` | High | Can modify ANY file |

### Bash Commands

| Permission | Risk Level | Implication |
|------------|------------|-------------|
| `Bash(git *)` | Low | Git operations only |
| `Bash(ls *)` | Low | Directory listing only |
| `Bash(cat *)` | Medium | Can read any file via cat |
| `Bash(rm *)` | Critical | Can delete files |
| `Bash(*)` | Critical | Full shell access |

**Bash is the most dangerous permission.** With `Bash(*)`:
- Install/uninstall software
- Modify system files
- Access environment variables (API keys)
- Network operations
- Execute arbitrary code

### MCP Servers

| Permission | Risk Level | Implication |
|------------|------------|-------------|
| `mcp__server__specific_tool` | Varies | Only that one tool |
| `mcp__server` | Higher | ALL tools from that server |

**Before allowing an MCP server, understand what tools it provides.**

Example for WhatsApp MCP:
- `mcp__whatsapp__list_chats` - Low risk (read chats)
- `mcp__whatsapp__send_message` - Medium risk (can message contacts)
- `mcp__whatsapp` - Grants ALL WhatsApp operations

## Best Practices

### 1. Principle of Least Privilege
Start with minimal permissions. Add more only when:
- You understand what the permission enables
- You've seen Reeve request it during normal operation
- The benefit outweighs the security cost

### 2. Use Path Restrictions
Instead of `Read(*)`, use `Read([YOUR_DESK_PATH]/*)`
Instead of `Write(*)`, use `Write([YOUR_DESK_PATH]/*)`

### 3. Audit Bash Permissions Carefully
If you need Bash, be specific:
```json
"Bash(git status)",
"Bash(git add [YOUR_DESK_PATH]/*)",
"Bash(git commit -m *)"
```

Not:
```json
"Bash(*)"  // TOO BROAD
```

### 4. Review MCP Tool Permissions
For each MCP server, list its tools and allow only needed ones:
```bash
# See what tools an MCP provides in the Claude Code session
# Then allow specific tools, not the whole server
```

### 5. Version Control Your Settings
The `.claude/settings.json` is tracked in git. Review changes:
```bash
git diff .claude/settings.json
git log -p .claude/settings.json
```

## Gradual Trust Building

**Week 1:** Start with Level 1 (default)
- Observe what permissions Reeve requests
- Approve manually, learn the patterns

**Week 2:** Move to Level 2 or 3
- If Reeve consistently needs to read/write Desk files
- Add those specific permissions

**Week 3+:** Expand based on usage
- Add git commands if you want auto-commits
- Add specific MCP tools as needed

**Full autonomy (Level 5):** Only after:
- You understand Reeve's behavior patterns
- You trust the system
- You have good git hygiene (can rollback mistakes)
- You've reviewed the security implications

## Troubleshooting

### "Permission denied" for expected operation
1. Check `.claude/settings.json` syntax
2. Verify the exact tool name and pattern
3. Test with a more permissive pattern, then restrict

### Reeve asking for approval too often
Consider adding commonly-approved operations to settings.json
But first ask: "Should this really be automatic?"

### Unexpected file changes
1. Check git history: `git log -p`
2. Review permissions: did you grant `Write(*)`?
3. Consider restricting to specific paths

## Reference: Common Permission Patterns

```json
// Read only your Desk
"Read([YOUR_DESK_PATH]/*)"

// Write only to Diary
"Write([YOUR_DESK_PATH]/Diary/*)"

// Git operations (safe subset)
"Bash(git status)",
"Bash(git add [YOUR_DESK_PATH]/*)",
"Bash(git commit -m *)",
"Bash(git log*)",
"Bash(git diff*)"

// Web search (no web fetch)
"WebSearch"

// Specific MCP tools
"mcp__telegram-notifier__send_notification",
"mcp__pulse-queue__schedule_pulse"
```

---

**Remember:** Every permission is a security decision. When in doubt, start restricted and expand thoughtfully. You can always add permissions later; removing them after a breach is too late.

## Related

- To understand the stack these permissions control, see `architecture` skill
- For initial setup walkthrough, see `onboarding` skill

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/reubenjohn) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
