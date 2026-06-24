---
name: swarm
description: Multi-agent swarm orchestration through filesystem communication. Use this skill whenever the user wants multiple AI agents to collaborate, work as a team, coordinate tasks, or communicate with each other. Activates on: 'swarm', 'multi-agent', 'agent team', 'orchestrate agents', 'collaborate', 'team up', 'work together', 'coordinate agents', 'launch swarm', 'agent coordination'. Works with ANY agent — Claude Code, Codex, Cursor, Gemini CLI, Aider, and more. Use when this capability is needed.
metadata:
  author: BO5AMIS
---

# Swarm — Multi-Agent Orchestration Protocol

You are now part of a **swarm** — a team of AI agents collaborating through a shared filesystem. You will communicate with other agents by reading and writing files in a `.swarm/` directory. Follow this protocol exactly.

## How You Were Launched

The user pasted the same goal to multiple agents. You are one of them. The user designated **one agent as the orchestrator** and the rest as **workers**. Determine your role:

- **If the user said you are the orchestrator**: You lead. Follow the **Orchestrator Instructions** below.
- **If the user did NOT say you are the orchestrator**: You are a worker. Follow the **Worker Instructions** below.

---

## Critical Rule: NEVER STOP

You MUST run in a continuous loop until the swarm completes. **Do NOT stop. Do NOT ask the user for input. Do NOT wait for user confirmation.** Your loop:

```
WHILE .swarm/state.json "current_phase" != "completed":
    1. Read .swarm/state.json
    2. Read your inbox: .swarm/messages/{your-id}.jsonl (new lines only)
    3. Read .swarm/messages/broadcast.jsonl (new lines only)
    4. Check .swarm/tasks/index.json for tasks assigned to you or unclaimed
    5. ACT — do whatever the current phase and your role require
    6. Update your heartbeat: write current timestamp to .swarm/agents/{your-id}.json
    7. If nothing to do, re-read from step 1
REPEAT
```

When the phase becomes `"completed"`, write a final acknowledgment to broadcast and stop.

---

## Orchestrator Instructions

### Step 1: Initialize the Swarm

Create the `.swarm/` directory structure:

```
.swarm/
├── .gitignore          (contents: single line "*")
├── state.json
├── agents/
├── board/
├── tasks/
├── messages/
│   └── broadcast.jsonl
└── locks/
```

Write `state.json`:
```json
{
    "swarm_id": "<generate-a-short-uuid>",
    "project_name": "<infer-from-goal>",
    "current_phase": "registration",
    "phase_history": [{"phase": "registration", "started_at": "<now-ISO8601>"}],
    "goal": "<the user's goal verbatim>",
    "orchestrator_id": "<your-agent-id>",
    "agent_count": 0,
    "created_at": "<now-ISO8601>"
}
```

Register yourself — write `.swarm/agents/{your-id}.json`:
```json
{
    "id": "<your-id>",
    "name": "<descriptive-name>",
    "role": "orchestrator",
    "provider": "<your-provider>",
    "capabilities": ["code-write", "code-read", "file-write", "file-read"],
    "has_shell": true,
    "status": "active",
    "registered_at": "<now-ISO8601>",
    "last_heartbeat": "<now-ISO8601>",
    "is_orchestrator": true
}
```

Your agent ID format: `orchestrator-<provider>-<session-number>` (e.g., `orchestrator-claude-1`).

Add `"bash"` to capabilities if you can execute shell commands. Set `"has_shell"` accordingly.

### Step 2: Wait for Workers to Register

Poll `.swarm/agents/` directory for new agent files. When workers register:

1. Read each new agent file
2. Decide their role based on the goal (see [Roles Reference](references/ROLES.md))
3. Write a `role-assignment` message to their inbox (`.swarm/messages/{agent-id}.jsonl`)
4. Update `agent_count` in `state.json`

Wait until you have at least **1 worker** registered and assigned a role, or **30 seconds** have passed (proceed with whoever registered).

### Step 3: Advance Through Phases

Transition phases by updating `state.json` and broadcasting a `phase-change` message. Follow this sequence:

| Phase | Name | What Happens |
|-------|------|-------------|
| 0 | `registration` | Agents register and get roles |
| 1 | `prd` | Create and approve the PRD |
| 2 | `tasks` | Break PRD into tasks, assign them |
| 3 | `implementation` | Agents build the feature |
| 4 | `review` | Testing and code review |
| 5 | `pr` | Create the pull request |
| done | `completed` | Swarm shuts down |

See [Phases Reference](references/PHASES.md) for detailed entry/exit criteria.

**Phase transition protocol:**
1. Update `state.json` with new `current_phase` and append to `phase_history`
2. Write a `phase-change` broadcast message
3. Wait for `phase-ack` messages from all active agents before proceeding with phase work

### Step 4: Monitor the Swarm

On every loop iteration:
- Check agent heartbeats. If any agent's `last_heartbeat` is >60 seconds old, mark them `"stale"` and reclaim their tasks
- Track task completion. When a blocking task completes, update dependent tasks in `index.json`
- Handle messages from workers (questions, completion reports, review results)

---

## Worker Instructions

### Step 1: Register

Check if `.swarm/` directory exists. If not, wait and re-check (the orchestrator creates it).

Once it exists, read `state.json` to understand the goal. Then register yourself — write `.swarm/agents/{your-id}.json`:

```json
{
    "id": "<your-id>",
    "name": "<descriptive-name>",
    "role": "pending",
    "provider": "<your-provider>",
    "capabilities": ["code-write", "code-read", "file-write", "file-read"],
    "has_shell": false,
    "status": "active",
    "registered_at": "<now-ISO8601>",
    "last_heartbeat": "<now-ISO8601>",
    "is_orchestrator": false
}
```

Your agent ID format: `worker-<provider>-<session-number>` (e.g., `worker-codex-2`).

Create your inbox file: `.swarm/messages/{your-id}.jsonl` (empty file).

Write a registration announcement to `broadcast.jsonl`:
```json
{"id":"<uuid>","from":"<your-id>","to":"all","type":"registration","phase":"registration","content":"Agent <your-id> registered and ready for role assignment.","references":[],"timestamp":"<now-ISO8601>"}
```

### Step 2: Wait for Role Assignment

Poll your inbox for a `role-assignment` message. When received:
1. Update your agent file with the assigned `role`
2. Write a `phase-ack` message to the orchestrator's inbox
3. Begin acting according to your role (see [Roles Reference](references/ROLES.md))

### Step 3: Follow the Loop

Execute the main loop described in **Critical Rule: NEVER STOP** above. On each iteration:

**During `prd` phase:**
- Read the PRD draft from `.swarm/board/`
- Write your review based on your role's expertise
- Send `approval` or `revision-request` to orchestrator

**During `tasks` phase:**
- Read task assignments from your inbox
- Acknowledge with a `task-update` message

**During `implementation` phase:**
- Work on your assigned tasks
- Write actual code to the project (not to .swarm/)
- Send `task-update` on start, `task-complete` on finish
- If blocked, send a `discussion` message to the orchestrator

**During `review` phase:**
- If you're a reviewer: review completed code, write feedback
- If you're an implementer: address review feedback, fix issues

**During `pr` phase:**
- Acknowledge the PR creation
- Provide any final input if requested

---

## Message Format

Every message is a single JSON line appended to a `.jsonl` file. Format:

```json
{"id":"<uuid>","from":"<sender-id>","to":"<recipient-id-or-all>","type":"<message-type>","phase":"<current-phase>","content":"<message-text>","references":[],"timestamp":"<ISO8601>"}
```

**To send a message:** Append one JSON line to `.swarm/messages/{recipient-id}.jsonl`
**To broadcast:** Append to `.swarm/messages/broadcast.jsonl`
**To read messages:** Read your inbox file, process only lines you haven't seen before (track line count)

### Message Types

| Type | Direction | Purpose |
|------|-----------|---------|
| `registration` | Worker → Broadcast | Announce presence |
| `role-assignment` | Orchestrator → Worker | Assign a role |
| `phase-change` | Orchestrator → Broadcast | Announce phase transition |
| `phase-ack` | Worker → Orchestrator | Acknowledge phase transition |
| `task-assignment` | Orchestrator → Worker | Assign a task |
| `task-update` | Worker → Orchestrator | Report progress |
| `task-complete` | Worker → Orchestrator | Mark task done |
| `review-request` | Any → Any | Request review |
| `review-response` | Any → Any | Provide review feedback |
| `approval` | Any → Orchestrator | Approve a document or task |
| `revision-request` | Any → Orchestrator | Request changes |
| `discussion` | Any → Any | Free-form discussion |
| `shutdown` | Orchestrator → Broadcast | Swarm is done |

See [Protocol Reference](references/PROTOCOL.md) for full JSON schemas.

---

## Task System

Tasks live in `.swarm/tasks/`. The orchestrator creates them during the `tasks` phase.

**`index.json`** — Master list (only orchestrator writes this):
```json
{
    "tasks": [
        {"id": "task-001", "title": "...", "status": "pending", "assigned_to": null, "blocked_by": []},
        {"id": "task-002", "title": "...", "status": "pending", "assigned_to": null, "blocked_by": ["task-001"]}
    ],
    "last_updated": "<ISO8601>"
}
```

**Individual task files** (`task-001.json`, etc.) — contain full details. See [Protocol Reference](references/PROTOCOL.md) for schema.

### Claiming a Task
1. Check that no `.swarm/locks/task-{id}.lock` file exists
2. Create `.swarm/locks/task-{id}.lock` containing your agent ID
3. If the file already exists (another agent claimed it), skip and find another task
4. Update the task file with your ID and `"status": "in_progress"`
5. Send a `task-update` message to the orchestrator

---

## Generating Your Agent ID

Use this format to avoid collisions:
- Orchestrator: `orchestrator-<provider>-1` (e.g., `orchestrator-claude-1`)
- Workers: `worker-<provider>-<N>` where N is the count of existing agent files + 1

Determine your provider from your model:
- Claude Code → `claude`
- Codex → `codex`
- Cursor → `cursor`
- Gemini CLI → `gemini`
- Aider → `aider`
- Other → `agent`

---

## Consensus Rules

- **PRD approval**: Majority of agents approve → proceed. Max 2 revision rounds. After round 2, orchestrator finalizes and notes unresolved concerns.
- **Code review**: All designated reviewers must approve before PR phase.
- **Task completion**: Task is complete when implementer marks it done AND reviewer approves.

---

## Quick Reference

| What | Where |
|------|-------|
| Global state | `.swarm/state.json` |
| Agent registration | `.swarm/agents/{id}.json` |
| Your inbox | `.swarm/messages/{your-id}.jsonl` |
| Broadcast channel | `.swarm/messages/broadcast.jsonl` |
| Board documents | `.swarm/board/` |
| Task list | `.swarm/tasks/index.json` |
| Task details | `.swarm/tasks/task-{id}.json` |
| Lock files | `.swarm/locks/task-{id}.lock` |
| PRD template | [templates/prd.md](templates/prd.md) |
| Task template | [templates/task.md](templates/task.md) |
| Full protocol | [references/PROTOCOL.md](references/PROTOCOL.md) |
| Role details | [references/ROLES.md](references/ROLES.md) |
| Phase details | [references/PHASES.md](references/PHASES.md) |
| Examples | [references/EXAMPLES.md](references/EXAMPLES.md) |

---
> Source: [BO5AMIS/swarm](https://github.com/BO5AMIS/swarm) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
