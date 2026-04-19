---
name: team-communicator
description: Communication protocols and coordination patterns for agent teams. Use when orchestrating multi-agent workflows, managing handoffs, or resolving coordination issues. Use when this capability is needed.
metadata:
  author: win10ogod
---

# Team Communicator

This skill provides communication protocols and coordination patterns for multi-agent teams using the SendMessage tool. It covers message type selection, coordination patterns, idle management, and anti-patterns to avoid.

## Message Type Selection Guide

Choose the correct message type based on your intent:

### Direct Message (`type: "message"`)

Use for **all routine communication**. This is your default.

- Status updates to a specific teammate or the team lead
- Task handoffs between two agents
- Review requests directed at a specific reviewer
- Follow-up questions about a task
- Reporting completion of assigned work
- Asking for help from a specific teammate

```json
{
  "type": "message",
  "recipient": "backend-dev",
  "content": "I've finished the API schema in /src/api/schema.ts. Ready for you to implement the endpoints.",
  "summary": "API schema complete, ready for endpoints"
}
```

### Broadcast (`type: "broadcast"`)

Use **only for critical team-wide issues**. Broadcasts are expensive -- each one sends a separate message to every teammate.

Valid broadcast scenarios:
- A blocking bug that halts all work (e.g., build is broken, shared dependency is corrupted)
- A fundamental change in project direction from the user
- A critical discovery that changes assumptions for everyone

```json
{
  "type": "broadcast",
  "content": "BLOCKING: The main branch build is broken due to a missing dependency. Do not push until resolved.",
  "summary": "Critical blocking issue found"
}
```

**Never broadcast for**: routine progress updates, questions for one person, task completions, non-blocking issues.

### Shutdown Request (`type: "shutdown_request"`)

Use when a teammate's work is complete and they should wind down.

```json
{
  "type": "shutdown_request",
  "recipient": "researcher",
  "content": "All research tasks are complete. Thank you for your work."
}
```

### Shutdown Response (`type: "shutdown_response"`)

Use when you receive a shutdown request. You **must** respond with the tool -- simply acknowledging in text is not enough.

```json
{
  "type": "shutdown_response",
  "request_id": "abc-123",
  "approve": true
}
```

Reject if you still have pending work:

```json
{
  "type": "shutdown_response",
  "request_id": "abc-123",
  "approve": false,
  "content": "Still running integration tests, need approximately 3 more minutes."
}
```

### Plan Approval Response (`type: "plan_approval_response"`)

Use when a teammate with `planModeRequired` submits a plan for your review.

```json
{
  "type": "plan_approval_response",
  "request_id": "abc-123",
  "recipient": "junior-dev",
  "approve": true
}
```

Reject with actionable feedback:

```json
{
  "type": "plan_approval_response",
  "request_id": "abc-123",
  "recipient": "junior-dev",
  "approve": false,
  "content": "Plan needs error handling for the database connection step. Add retry logic with exponential backoff."
}
```

## Communication Protocols

### Status Updates

Report progress proactively. Do not wait to be asked.

**When**: After completing a meaningful unit of work, when encountering a blocker, or at natural checkpoints.

**Format**: What is done, what is next, any blockers.

```json
{
  "type": "message",
  "recipient": "team-lead",
  "content": "Status: Completed user authentication module (3 files). Next: Starting session management. No blockers.",
  "summary": "Auth module done, starting sessions"
}
```

### Task Handoffs

Transfer work between agents with full context so the receiver can continue without guessing.

**Required elements**: What was done, what files were changed, what remains, acceptance criteria.

```json
{
  "type": "message",
  "recipient": "tester",
  "content": "Handoff: User registration endpoint is implemented.\n\nFiles changed:\n- /src/api/routes/register.ts (new endpoint)\n- /src/models/user.ts (added validation)\n- /src/middleware/auth.ts (updated token generation)\n\nRemaining: Write integration tests covering signup flow, duplicate email handling, and password strength validation.\n\nAcceptance: All tests pass, edge cases for malformed input are covered.",
  "summary": "Registration endpoint ready for testing"
}
```

### Review Requests

Ask for review with enough context for the reviewer to be effective.

```json
{
  "type": "message",
  "recipient": "senior-dev",
  "content": "Review request: Database migration script at /migrations/003_add_indexes.sql.\n\nContext: Adding indexes to improve query performance on the users and orders tables.\n\nConcerns: Unsure if the composite index on (user_id, created_at) is optimal for our query patterns.\n\nPlease check: Index strategy, potential locking issues during migration, rollback safety.",
  "summary": "Review needed for DB migration script"
}
```

### Escalation

Escalate to the team lead when you are blocked, when you discover a problem outside your scope, or when a decision requires authority you do not have.

```json
{
  "type": "message",
  "recipient": "team-lead",
  "content": "Escalation: I need a decision on the caching strategy.\n\nContext: The API response times exceed the 200ms target. I see two viable approaches:\n1. Redis cache with 5-minute TTL (faster, more infrastructure)\n2. In-memory cache with LRU eviction (simpler, limited to single instance)\n\nI cannot proceed with the performance optimization task until this is decided.",
  "summary": "Need decision on caching strategy"
}
```

## Coordination Patterns

### Producer-Consumer

One agent produces artifacts that another agent consumes. Best for pipeline workflows.

**Flow**: Producer completes work and sends a handoff message. Consumer acknowledges receipt and begins processing. Consumer reports back when done.

Example: `code-gen` writes implementation, `tester` writes tests for it.

The producer sends a handoff with file paths and context. The consumer acknowledges, does the work, and reports results. If the consumer finds issues, they message the producer with specifics rather than fixing the code themselves (unless authorized to do so).

### Review Chain

Sequential review where each reviewer adds feedback before passing to the next. Best for quality gates.

**Flow**: Author sends to first reviewer. Reviewer approves or requests changes. On approval, author (or lead) sends to next reviewer. Process continues until all reviewers approve.

Example: `implementer` writes code, `reviewer` checks logic, `security-auditor` checks vulnerabilities.

### Fan-out/Fan-in

The lead distributes independent subtasks to multiple agents, then collects and aggregates results.

**Flow**: Lead sends individual task messages to each agent. Agents work in parallel and report completion. Lead tracks progress and aggregates results once all agents finish.

Example: Lead assigns different API endpoints to three developers. Each works independently. Lead collects completion reports and runs integration tests.

### Collaborative Editing

Multiple agents work on different parts of the same artifact. Requires coordination to avoid conflicts.

**Flow**: Lead assigns sections. Agents claim their sections before starting. Agents work on claimed sections only. On completion, agents notify the lead. Lead handles integration.

This pattern requires a claim protocol -- see the conflict resolution reference for details.

### Watchdog Pattern

A monitor agent periodically checks on long-running workers.

**Flow**: Monitor sends check-in messages at regular intervals. Workers respond with status. If a worker fails to respond after two attempts, the monitor escalates to the lead.

### Relay Pattern

A chain of agents where the output of one feeds the next, like a transformation pipeline.

**Flow**: Each agent in the chain completes its transformation and hands off to the next agent. Errors propagate backward to allow earlier stages to retry or adjust.

See `references/coordination-patterns.md` for detailed setup, message flows, and error handling for each pattern.

## Idle Management

Teammates that appear idle are **waiting for work or messages** -- they are not dead or broken. The system delivers messages automatically.

### Guidelines

- **Do not panic** when a teammate shows as idle. They are simply waiting.
- **Send a message** if you have work for them. They will receive it and respond.
- **Check in** if an agent has been idle for an unexpectedly long time relative to their assigned task. A simple status request is sufficient.
- **Escalate** only if an agent fails to respond after two direct messages spaced apart.

### Check-in Message

```json
{
  "type": "message",
  "recipient": "data-processor",
  "content": "Checking in: Are you still working on the CSV import task? Please send a status update.",
  "summary": "Status check on CSV import task"
}
```

### When Not to Check In

- The agent just received a large task (give them time to work)
- You sent them a message recently and are awaiting response
- They are not assigned to any of your tasks

## Broadcast Rules

Broadcasts are the most expensive message type. Follow these rules strictly.

1. **Ask yourself**: Does every single teammate need this information right now?
2. **If only some teammates need it**: Send individual messages to those teammates.
3. **If it can wait**: Send it as a DM to the lead and let them distribute.
4. **If it is truly critical and team-wide**: Broadcast it.

### Legitimate Broadcast Scenarios

- The repository is in a broken state that affects everyone
- The user has changed project requirements fundamentally
- A shared resource (database, API, build system) is down
- A security vulnerability has been discovered in shared code

### Never Broadcast

- Task completion announcements (tell the lead or the next agent in the chain)
- Questions (ask the specific person who can answer)
- Progress updates (tell your lead)
- Non-blocking issues (tell the affected parties)

## Message Formatting

### Keep Messages Concise

Agents process information efficiently. Do not pad messages with pleasantries or redundant context.

**Good**: "Completed auth module. Files: /src/auth/login.ts, /src/auth/token.ts. Ready for review."

**Bad**: "Hi there! I wanted to let you know that I've been working really hard on the authentication module and I'm happy to report that I've finished it. The files I worked on are..."

### Include Actionable Context

Every message should make it clear what the recipient needs to do next.

- If reporting status: state what you are doing next or what you need
- If handing off work: include files, remaining tasks, and acceptance criteria
- If requesting review: specify what to look for and where
- If escalating: present options and explain what decision you need

### Use Structured Formats for Handoffs

For task handoffs, use a consistent structure:

```
Handoff: [brief description]

Files: [list of relevant file paths]
Done: [what was completed]
Remaining: [what the recipient needs to do]
Acceptance: [how to know the work is done]
Notes: [any gotchas or important context]
```

## Anti-patterns

### Over-Broadcasting

Sending broadcasts for routine updates. This wastes resources and trains teammates to ignore broadcasts, which means they may miss genuinely critical ones.

### Message Storms

Sending many rapid messages to the same agent without waiting for responses. This overwhelms the recipient and creates confusion about which message to address first. Batch your information into a single, well-structured message.

### Ignoring Idle States

Repeatedly messaging an idle agent or escalating immediately when they do not respond. Idle agents are waiting, not broken. Give them time before escalating.

### Blocking on Responses

Sitting idle while waiting for a response instead of continuing with other available work. If you are waiting on agent A, check if there is other work you can do in the meantime. Only report yourself as blocked if you genuinely have no other tasks.

### Fire-and-Forget Handoffs

Handing off work without sufficient context, forcing the receiving agent to guess or ask follow-up questions. Always include file paths, acceptance criteria, and relevant context.

### Shadow Communication

Making decisions or doing work based on assumptions about what another agent is doing without actually messaging them. When in doubt, send a quick DM to confirm.

### Premature Escalation

Escalating to the team lead before attempting to resolve an issue directly with the involved teammate. Try direct communication first. Escalate only when direct resolution fails or when the issue requires authority.

## Related Skills

- **team-orchestrator** -- Coordinates task assignments and workflows that drive communication needs
- **team-doctor** -- Diagnose and fix communication breakdowns
- **team-monitor** -- Detect idle or stuck agents that may need a wake-up message

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/win10ogod) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
