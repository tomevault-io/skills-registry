---
name: transcript-analyzer
description: Analyze Claude Code session transcripts to debug plugins, understand context usage, and trace execution flow Use when this capability is needed.
metadata:
  author: bfreis
---

# Transcript Analyzer Skill

Use this skill when you need to analyze Claude Code session transcripts for:
- Debugging plugin behavior
- Understanding context/token usage patterns
- Tracing tool execution flow
- Finding sources of context bloat
- Investigating errors or unexpected behavior

## Transcript Location

Claude Code stores session transcripts at:
```
~/.claude/projects/-PATH-TO-PROJECT/*.jsonl
```

The path uses dashes instead of slashes. For example:
- Project: `/Users/bfreis/dev/myproject`
- Transcripts: `~/.claude/projects/-Users-bfreis-dev-myproject/*.jsonl`

## Transcript Structure

Transcripts use **JSONL format** (one JSON object per line).

### Record Types

| Type | Description |
|------|-------------|
| `summary` | Session metadata |
| `file-history-snapshot` | File change tracking |
| `user` | User messages (includes tool results) |
| `assistant` | Assistant messages (includes tool calls) |

### Message Structure

```json
{
  "type": "user" | "assistant",
  "uuid": "message-uuid",
  "parentUuid": "parent-message-uuid",
  "sessionId": "session-uuid",
  "isSidechain": false,
  "timestamp": "2025-12-06T...",
  "message": {
    "role": "user" | "assistant",
    "content": [...],
    "usage": { "input_tokens": N, "output_tokens": N }
  }
}
```

### Content Block Types

**In assistant messages:**
- `text` - Regular text response
- `tool_use` - Tool invocation with `.id`, `.name`, `.input`
- `thinking` - Extended thinking blocks

**In user messages:**
- `text` - User input
- `tool_result` - Tool output with `.tool_use_id`, `.content`

### Critical Gotchas

1. **Content is ALWAYS an array** - Even single text blocks
2. **Tool result `.content` can be string OR array** - Handle both:
   ```jq
   if .content | type == "array" then
     (.content | map(.text // "") | add)
   else
     .content
   end
   ```
3. **Use `[]?` not `[]`** - Handles missing fields gracefully
4. **Sub-agents have `isSidechain: true`** - Filter these for main conversation only
5. **`parentUuid` links threads** - Not line order

## Using transcript-tool

The skill provides a CLI at `scripts/transcript-tool`:

```bash
# Quick overview
transcript-tool summary session.jsonl

# Find context bloat sources
transcript-tool bloat session.jsonl 15

# Tool usage breakdown
transcript-tool tools session.jsonl

# Trace specific tool
transcript-tool trace-tool session.jsonl Read

# Find errors
transcript-tool errors session.jsonl

# Custom jq query
transcript-tool extract session.jsonl '.type'
```

## Common Analysis Workflows

### 1. Debug Plugin Behavior

```bash
# Find skill invocations
transcript-tool trace-skill session.jsonl plan-generator

# See what tools were used
transcript-tool tools session.jsonl

# Check for errors
transcript-tool errors session.jsonl
```

### 2. Investigate Context Bloat

```bash
# Find largest tool results
transcript-tool bloat session.jsonl 20

# Message size analysis
transcript-tool messages session.jsonl

# Identify specific large results
transcript-tool extract session.jsonl '
  select(.type == "user") |
  .message.content[]? |
  select(.type == "tool_result") |
  select((.content | tostring | length) > 10000) |
  .tool_use_id
'
```

### 3. Trace Execution Flow

```bash
# All tool calls in order
transcript-tool extract session.jsonl '
  select(.type == "assistant") |
  .message.content[]? |
  select(.type == "tool_use") |
  "\(.name): \(.input | keys | join(", "))"
'
```

## Raw jq Recipes

For complex analysis, use jq directly:

**Count content block types:**
```bash
jq -r '
  select(.type == "user" or .type == "assistant") |
  .message.content[]? |
  .type
' session.jsonl | sort | uniq -c
```

**Find tool call by ID:**
```bash
jq -r --arg id "toolu_xxx" '
  select(.type == "assistant") |
  .message.content[]? |
  select(.type == "tool_use" and .id == $id)
' session.jsonl
```

**Get corresponding tool result:**
```bash
jq -r --arg id "toolu_xxx" '
  select(.type == "user") |
  .message.content[]? |
  select(.type == "tool_result" and .tool_use_id == $id) |
  .content
' session.jsonl
```

**Token usage per message:**
```bash
jq -r '
  select(.type == "assistant" and .message.usage) |
  "\(.message.usage.input_tokens) in, \(.message.usage.output_tokens) out"
' session.jsonl
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bfreis) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
