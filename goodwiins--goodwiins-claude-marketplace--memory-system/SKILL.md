---
name: memory-system
description: | Use when this capability is needed.
metadata:
  author: goodwiins
---

# Memory System for Claude Code

Manage persistent memory across Claude Code sessions using Obsidian vault storage.

## Memory Architecture

Each project has its own isolated memory space. The project name is auto-detected from the git repository name (or folder name if not a git repo).

```
claude-memory/
└── projects/
    └── {project-name}/          # Isolated per-project memory
        ├── short-term/          # Current session context (cleared between major tasks)
        │   ├── current-task.md  # Active task details
        │   └── conversation-context.md  # User preferences, session state
        ├── long-term/           # Persistent knowledge (never auto-cleared)
        │   ├── decisions-log.md     # Architectural/design decisions
        │   ├── code-patterns.md     # Reusable code patterns
        │   ├── lessons-learned.md   # Bugs, gotchas, hard-won knowledge
        │   └── tools-reference.md   # Commands and tool references
        └── daily/               # Session logs by date
            └── YYYY-MM-DD.md    # Daily activity log
```

### Project Name Detection

The project name is determined by:
1. Git repository name: `basename $(git rev-parse --show-toplevel 2>/dev/null)`
2. Fallback: Current working directory name

## When to Save Memory

### Save to Long-term Memory

Trigger a save when discovering:

| Discovery | Save To | Tags |
|-----------|---------|------|
| Architectural decision | `claude-memory/projects/{project}/long-term/decisions-log.md` | `#decision` |
| Reusable code pattern | `claude-memory/projects/{project}/long-term/code-patterns.md` | `#pattern` |
| Bug fix or gotcha | `claude-memory/projects/{project}/long-term/lessons-learned.md` | `#bug #learning` |
| Tool/command reference | `claude-memory/projects/{project}/long-term/tools-reference.md` | `#reference` |

### Save to Short-term Memory

Update during active work:

| Event | Save To |
|-------|---------|
| Starting new task | `claude-memory/projects/{project}/short-term/current-task.md` |
| User preference discovered | `claude-memory/projects/{project}/short-term/conversation-context.md` |
| Session activity | `claude-memory/projects/{project}/daily/YYYY-MM-DD.md` |

## Memory Format

Use consistent formatting with metadata:

```markdown
## YYYY-MM-DD: Title

**Context**: Why this matters
**Decision/Pattern/Lesson**: What was learned
**Files**: Affected files (if applicable)
**Tags**: #decision #pattern #bug #learning
```

## Obsidian MCP Tools

Use these tools for memory operations:

| Operation | Tool |
|-----------|------|
| Save/append | `mcp__MCP_DOCKER__obsidian_append_content` |
| Read single file | `mcp__MCP_DOCKER__obsidian_get_file_contents` |
| Read multiple files | `mcp__MCP_DOCKER__obsidian_batch_get_file_contents` |
| Search memories | `mcp__MCP_DOCKER__obsidian_simple_search` |
| Get today's note | `mcp__MCP_DOCKER__obsidian_get_periodic_note` |
| Recent changes | `mcp__MCP_DOCKER__obsidian_get_recent_changes` |
| List directory | `mcp__MCP_DOCKER__obsidian_list_files_in_dir` |

## Session Workflows

### Start of Session

1. Read conversation context for user preferences
2. Check recent daily logs for continuity
3. Load project-specific memory if working on known project

```
Read: claude-memory/projects/{project}/short-term/conversation-context.md
Read: claude-memory/projects/{project}/daily/{recent-date}.md
```

### During Work

1. Update `current-task.md` when switching tasks
2. Append to daily log for significant actions
3. Save patterns/decisions as discovered

### End of Session

1. Summarize session in daily log
2. Save any important learnings to long-term memory
3. Update `current-task.md` with next steps

## Memory Categories Reference

### decisions-log.md
Architectural and design decisions with rationale:
- Technology choices
- Pattern selections
- Trade-off decisions
- Security decisions

### code-patterns.md
Reusable code snippets and patterns:
- Error handling patterns
- API patterns
- Component patterns
- Testing patterns

### lessons-learned.md
Bugs, gotchas, and hard-won knowledge:
- Bug root causes and fixes
- Common mistakes to avoid
- Environment-specific issues
- Integration gotchas

### tools-reference.md
Commands and tool quick reference:
- MCP tool usage
- CLI commands
- Development commands
- Debugging tips

## Best Practices

1. **Be specific**: Include file paths, error messages, concrete details
2. **Add context**: Explain why, not just what
3. **Use tags**: Enable searchability with consistent tags
4. **Date entries**: Always include date for temporal context
5. **Cross-reference**: Link related memories when applicable
6. **Keep it lean**: One concept per entry, expand in follow-ups

## Additional Resources

### Reference Files

For detailed patterns and templates:
- **`references/memory-templates.md`** - Entry templates for each category
- **`references/tagging-guide.md`** - Comprehensive tagging conventions

### Example Files

Working memory entries:
- **`examples/decision-entry.md`** - Sample decision log entry
- **`examples/pattern-entry.md`** - Sample pattern entry
- **`examples/lesson-entry.md`** - Sample lesson learned entry

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/goodwiins) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
