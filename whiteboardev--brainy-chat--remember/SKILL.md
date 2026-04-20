---
name: remember
description: Store and retrieve memories using the BrAIny REST API. Use this skill to persist facts, notes, and messages about the user or conversation. Automatically stores context when the context window is reaching its limit. Use when this capability is needed.
metadata:
  author: whiteboardev
---

# Remember Skill

Store and retrieve persistent memories via BrAIny API.

## CLI Script

Use the bundled script to interact with the API:

```bash
# Store a memory
bun scripts/memory.ts store "User prefers dark mode" --type fact

# Store context summary
bun scripts/memory.ts store "Working on auth refactor..." --type note

# Search memories
bun scripts/memory.ts retrieve "authentication" --limit 5

# Get recent memories
bun scripts/memory.ts retrieve --limit 10
```

### Environment Variables

| Variable | Purpose | Default |
|----------|---------|---------|
| `BRAINY_URL` | API base URL | `http://localhost:3000` |
| `BRAINY_USER_ID` | User ID for memory scoping | `default` |

## API Reference

### Store Memory

```http
POST /v1/users/{userId}/memories
Content-Type: application/json

{
  "content": "string",
  "type": "fact" | "note" | "message",
  "authorType": "agent" | "user"
}
```

**Memory Types**

| Type | Use Case |
|------|----------|
| `fact` | Persistent info about user (preferences, background) |
| `note` | Observations, summaries, context |
| `message` | Conversation excerpts worth remembering |

**Response**

```json
{
  "success": true,
  "memoryId": "uuid"
}
```

Duplicates (same user + content) are skipped automatically.

### Retrieve Memories

```http
GET /v1/users/{userId}/memories?query={text}&limit={n}
```

- With `query`: semantic search (vector similarity, falls back to keyword)
- Without `query`: returns recent memories

**Response**

```json
{
  "success": true,
  "memories": [
    {
      "id": "uuid",
      "content": "string",
      "type": "fact",
      "authorType": "agent",
      "createdAt": "2024-01-01T00:00:00Z",
      "similarity": 0.85
    }
  ],
  "searchMethod": "vector" | "keyword" | "recent"
}
```

## Context Window Preservation

When the context window is reaching its limit, store a comprehensive session summary. This is critical for maintaining continuity.

### What to Capture

Create a detailed record containing:

**Session Overview**
- What task or problem was being worked on
- The user's original request and goals
- Current progress and completion status

**Technical Context**
- Project structure and relevant directories
- Files read, created, or modified (with paths)
- Key code patterns, architectures, or conventions discovered
- Dependencies, configurations, and environment details
- Database schemas, API endpoints, or data structures involved

**Decisions and Rationale**
- Design decisions made and why
- Trade-offs considered
- Alternatives rejected and reasons
- User preferences expressed during the session

**Current State**
- What was just completed
- What is currently in progress
- Blockers or issues encountered
- Error messages or debugging findings

**Next Steps**
- Pending tasks not yet started
- Follow-up items identified
- Questions that need user input
- Planned approach for remaining work

### Storage Format

Store as a `note` with authorType `agent`. Structure the content clearly:

```json
{
  "content": "## Session Context\n\n### Task\n[Detailed description of what we're working on]\n\n### Progress\n- Completed: [list with details]\n- In progress: [current work]\n- Pending: [remaining items]\n\n### Technical Details\n- Project: [path and structure]\n- Files modified: [list with purposes]\n- Key patterns: [architectural notes]\n\n### Decisions\n- [Decision]: [Rationale]\n\n### Blockers\n- [Issue]: [Status and findings]\n\n### Next Steps\n1. [Specific action]\n2. [Specific action]",
  "type": "note",
  "authorType": "agent"
}
```

### When to Trigger

Store context preservation when:
- Context usage reaches 75% of the window
- Before starting a task that may exceed remaining context
- When switching between major subtasks
- Before any operation that might truncate history

## Usage Guidelines

1. Store facts when user shares preferences or personal info
2. Store notes to summarize important context
3. Store comprehensive context when nearing context limit
4. Retrieve memories at conversation start for continuity
5. Search relevant memories when context would help

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/whiteboardev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
