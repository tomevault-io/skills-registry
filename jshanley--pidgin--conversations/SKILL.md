---
name: conversations
description: Conventions for recording human-AI conversations in pidgin. Use when saving a conversation, reading past conversations, or working with the conversations/ directory. Use when this capability is needed.
metadata:
  author: jshanley
---

# Conversations

Records of human-AI exchanges, preserved verbatim.

## Source of Truth

Claude Code saves session transcripts as JSONL files in `~/.claude/projects/<project-path>/`. These are the authoritative record—every user message, assistant response, tool call, and result.

Conversation files in `conversations/` are derived from transcripts. They're a readable projection of the log.

**Transcript location for this project:**
```
~/.claude/projects/-Users-john-code-pidgin/*.jsonl
```

## Format

```markdown
# Title
date: 2026-01-31T10:00:00-08:00
follows: previous-conversation-name  (optional)
transcript: abc123-def456  (source transcript UUID)

---

## user

What they said, verbatim.

## assistant

What was said back, verbatim.

## tool (Read)

file_path: /path/to/file

[file contents as result]

## assistant

Response after seeing the file.
```

**Section types:**
- `## user` — human messages
- `## assistant` — Claude's text responses
- `## tool (Name)` — tool invocations with parameters, blank line, then result

## Extracting from Transcripts

**Preview a transcript:**
```bash
cat transcript.jsonl | jq -r 'select(.type == "user") |
  .message.content | if type == "array" then
  .[] | select(.type == "text") | .text[:80] else .[:80] end' |
  grep -v "^<" | head -10
```

**Extract a conversation:**
```bash
python scripts/transcript-to-conversation.py <transcript.jsonl> [start_text] > output.md
```

**What to include:**
- User text messages
- Assistant text responses
- Tool calls with their parameters and results

**What to skip:**
- `/clear` and other commands
- Thinking blocks (internal)

## Conventions

- **Location**: `conversations/`
- **Naming**: topic-based (`pidgin-origin.md`, not `01-origin.md`)
- **Date**: ISO 8601 with timezone (`2026-02-01T10:30:00-08:00`)
- **Content**: verbatim, no summary or interpretation
- **Boundaries**: save at natural breaks, when a thread feels complete

## Timestamps

```bash
date +"%Y-%m-%dT%H:%M:%S%z" | sed 's/\(..\)$/:\1/'
```

## Retrieval

- **"read the last conversation"** → read most recent by modification time
- **"read [name]"** → read the referenced file
- **"what were we talking about?"** → read the last conversation

## Properties

- Human-readable without tooling
- Parseable by simple scripts
- Git-friendly (diffable, mergeable)
- Traceable to source transcript

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jshanley) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
