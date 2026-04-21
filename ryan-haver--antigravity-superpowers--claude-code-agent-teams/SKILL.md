---
name: claude-code-agent-teams
description: Best practices for orchestrating Claude Code Agent Teams — coordinating multiple Claude Code instances working together with shared tasks, inter-agent messaging, and centralized management. Use this skill when spinning up multi-agent teams, deciding team composition, choosing display modes, or troubleshooting team coordination. Use when this capability is needed.
metadata:
  author: ryan-haver
---

# Claude Code Agent Teams Best Practices

> **Source**: [Claude Code Agent Teams Documentation](https://code.claude.com/docs/en/agent-teams.md)

> [!WARNING]
> Agent Teams are **experimental** and disabled by default. Enable via `settings.json`:
> ```json
> {
>   "env": {
>     "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
>   }
> }
> ```

## What Are Agent Teams?

Agent Teams let you coordinate **multiple independent Claude Code instances**. One session acts as the **team lead**, coordinating work, assigning tasks, and synthesizing results. Teammates work independently, each in its own context window, and communicate directly with each other.

---

## Subagents vs Agent Teams Decision Guide

| | Subagents | Agent Teams |
|---|---|---|
| **Context** | Own context; results return to caller | Own context; fully independent |
| **Communication** | Report results back to main agent only | Teammates message each other directly |
| **Coordination** | Main agent manages all work | Shared task list with self-coordination |
| **Best for** | Focused tasks where only the result matters | Complex work requiring discussion and collaboration |
| **Token cost** | Lower: results summarized back | Higher: each teammate is a separate Claude instance |

### Use **Subagents** when:
- You need quick, focused workers that report back
- Tasks don't need inter-agent coordination
- Token cost matters
- Work is sequential or nested

### Use **Agent Teams** when:
- Workers need to share findings and challenge each other
- Tasks benefit from parallel exploration
- Cross-layer coordination is needed (frontend + backend + tests)
- You're debugging with competing hypotheses

---

## Architecture

| Component | Role |
|-----------|------|
| **Team Lead** | Main session that creates the team, spawns teammates, and coordinates work |
| **Teammates** | Separate Claude Code instances working on assigned tasks |
| **Task List** | Shared list of work items with pending → in progress → completed states |
| **Mailbox** | Messaging system for communication between agents |

**Storage locations:**
- Team config: `~/.claude/teams/{team-name}/config.json`
- Task list: `~/.claude/tasks/{team-name}/`

---

## Best Use Cases

### 1. Research and Review
Multiple teammates investigate different aspects simultaneously, then share and challenge findings.
```
Create an agent team to review PR #142. Spawn three reviewers:
- One focused on security implications
- One checking performance impact
- One validating test coverage
Have them each review and report findings.
```

### 2. New Modules or Features
Each teammate owns a separate piece without stepping on each other.

### 3. Debugging with Competing Hypotheses
Teammates test different theories in parallel and converge faster.
```
Users report the app exits after one message instead of staying connected.
Spawn 5 agent teammates to investigate different hypotheses. Have them talk to
each other to try to disprove each other's theories, like a scientific debate.
```

### 4. Cross-Layer Coordination
Changes that span frontend, backend, and tests — each owned by a different teammate.

---

## Starting a Team

Tell Claude to create a team and describe the task + structure in natural language:

```
I'm designing a CLI tool that helps developers track TODO comments across
their codebase. Create an agent team to explore this from different angles: one
teammate on UX, one on technical architecture, one playing devil's advocate.
```

Claude creates the team, spawns teammates, and coordinates work based on your prompt.

---

## Display Modes

| Mode | Description | Requirements |
|------|-------------|-------------|
| **In-process** (default) | All teammates run inside your main terminal | Any terminal |
| **Split panes** | Each teammate gets its own pane | tmux or iTerm2 |
| **Auto** | Split panes if in tmux, otherwise in-process | — |

Configure in `settings.json`:
```json
{
  "teammateMode": "in-process"
}
```

Or per-session:
```bash
claude --teammate-mode in-process
```

> [!NOTE]
> Split-pane mode is **not supported** in VS Code's integrated terminal, Windows Terminal, or Ghostty. Works in WSL with tmux.

---

## Best Practices

### 1. Give Teammates Enough Context
Teammates load project context (CLAUDE.md, MCP servers, skills) but **don't inherit the lead's conversation history**. Include task-specific details in the spawn prompt:
```
Spawn a security reviewer teammate with the prompt: "Review the authentication
module at src/auth/ for security vulnerabilities. Focus on token handling,
session management, and input validation. The app uses JWT tokens stored in
httpOnly cookies. Report any issues with severity ratings."
```

### 2. Size Tasks Appropriately
- **Too small**: Coordination overhead exceeds the benefit
- **Too large**: Teammates work too long without check-ins
- **Just right**: Self-contained units that produce a clear deliverable

> [!TIP]
> Aim for **5-6 tasks per teammate**. This keeps everyone productive and lets the lead reassign work if someone gets stuck.

### 3. Avoid File Conflicts
Two teammates editing the same file leads to overwrites. **Break work so each teammate owns different files.**

### 4. Wait for Teammates to Finish
If the lead starts implementing instead of waiting:
```
Wait for your teammates to complete their tasks before proceeding
```

### 5. Use Delegate Mode for Pure Coordination
Press **Shift+Tab** to restrict the lead to coordination-only tools (spawning, messaging, shutting down teammates, managing tasks). No code-touching.

### 6. Require Plan Approval for Risky Work
```
Spawn an architect teammate to refactor the authentication module.
Require plan approval before they make any changes.
```
The lead reviews and approves/rejects plans autonomously.

### 7. Monitor and Steer
Check in on teammates' progress, redirect approaches that aren't working, and synthesize findings. **Don't let a team run unattended for too long.**

### 8. Start with Research and Review
If new to agent teams, start with non-code-writing tasks: reviewing a PR, researching a library, investigating a bug. These show the value of parallel exploration without file-conflict risks.

---

## Controlling the Team

### Specify Teammates and Models
```
Create a team with 4 teammates to refactor these modules in parallel.
Use Sonnet for each teammate.
```

### Talk to Teammates Directly
- **In-process mode**: **Shift+Up/Down** to select teammate, type to send message
- **Enter** to view a teammate's session, **Escape** to interrupt
- **Ctrl+T** to toggle the task list
- **Split-pane mode**: Click into a teammate's pane

### Assign and Claim Tasks
- **Lead assigns**: Tell the lead which task to give to which teammate
- **Self-claim**: After finishing, teammates pick up the next unassigned, unblocked task
- Tasks support dependencies: blocked tasks unblock automatically when dependencies complete

### Shut Down Teammates
```
Ask the researcher teammate to shut down
```

### Clean Up the Team
```
Clean up the team
```

> [!CAUTION]
> **Always use the lead to clean up.** Teammates should not run cleanup — their team context may not resolve correctly.

---

## Enforce Quality Gates with Hooks

| Hook Event | When It Fires | Usage |
|------------|---------------|-------|
| `TeammateIdle` | Teammate about to go idle | Exit code 2 → send feedback and keep working |
| `TaskCompleted` | Task being marked complete | Exit code 2 → prevent completion and send feedback |

---

## Key Controls Reference

| Action | Shortcut/Command |
|--------|-----------------|
| Select teammate | **Shift+Up/Down** |
| Toggle delegate mode | **Shift+Tab** |
| Toggle task list | **Ctrl+T** |
| View teammate session | **Enter** (on selected teammate) |
| Interrupt teammate | **Escape** (in teammate view) |
| Force in-process mode | `claude --teammate-mode in-process` |

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Teammates not appearing | Press **Shift+Down** to cycle; verify task complexity warrants a team |
| Too many permission prompts | Pre-approve common operations in permission settings before spawning |
| Teammates stopping on errors | Check output and give additional instructions, or spawn replacement |
| Lead shuts down too early | Tell lead to "wait for teammates to finish before proceeding" |
| Orphaned tmux sessions | `tmux ls` then `tmux kill-session -t <name>` |

---

## Known Limitations

- **No session resumption** for in-process teammates (use `/resume` cautiously)
- **Task status can lag**: manually update if work appears stuck
- **One team per session**: clean up before starting a new team
- **No nested teams**: teammates cannot spawn their own teams
- **Lead is fixed**: cannot promote a teammate to lead
- **Permissions set at spawn**: override per-teammate after spawning
- **Split panes require tmux or iTerm2**: not available in VS Code terminal or Windows Terminal

---

## Token Cost Awareness

Agent teams use **significantly more tokens** than a single session. Each teammate has its own context window and token usage scales with team size.

| Scenario | Recommendation |
|----------|---------------|
| Research, review, new features | Worth the extra tokens |
| Routine tasks | Single session is more cost-effective |
| Large team (5+ teammates) | Use `broadcast` sparingly (costs scale with team size) |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ryan-haver) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
