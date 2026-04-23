---
name: remembering-conversations
description: Search previous Claude Code conversations for facts, patterns, decisions, and context using semantic or text search Use when this capability is needed.
metadata:
  author: obra
---

# Remembering Conversations

Search archived conversations using semantic similarity or exact text matching.

**Core principle:** Search before reinventing.

**Announce:** "I'm searching previous conversations for [topic]."

**Setup:** See INDEXING.md

## When to Use

**Search when:**
- Your human partner mentions "we discussed this before"
- Debugging similar issues
- Looking for architectural decisions or patterns
- Before implementing something familiar

**Don't search when:**
- Info in current conversation
- Question about current codebase (use Grep/Read)

## In-Session Use

**Always use subagents** (50-100x context savings). See skills/getting-started for workflow.

**Manual/CLI use:** Direct search (below) for humans outside Claude Code sessions.

## Direct Search (Manual/CLI)

**Tool:** `~/.claude/skills/collaboration/remembering-conversations/tool/search-conversations`

**Modes:**
```bash
search-conversations "query"              # Vector similarity (default)
search-conversations --text "exact"       # Exact string match
search-conversations --both "query"       # Both modes
```

**Flags:**
```bash
--after YYYY-MM-DD    # Filter by date
--before YYYY-MM-DD   # Filter by date
--limit N             # Max results (default: 10)
--help                # Full usage
```

**Examples:**
```bash
# Semantic search
search-conversations "React Router authentication errors"

# Find git SHA
search-conversations --text "a1b2c3d4"

# Time range
search-conversations --after 2025-09-01 "refactoring"
```

Returns: project, date, conversation summary, matched exchange, similarity %, file path.

**For details:** Run `search-conversations --help`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/obra) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
