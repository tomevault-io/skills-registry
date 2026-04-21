---
name: conversation-history-setup
description: Guide users through initial setup of conversation history indexing. Use when user asks about setting up conversation history, indexing past conversations, or when search returns no results and the database may be empty. Use when this capability is needed.
metadata:
  author: dhughes
---

# Conversation History Setup

This skill helps users get started with the conversation-history plugin.

## When to Use

Invoke this guidance when:
- User asks how to set up conversation history
- User asks about indexing past/historical conversations
- Search returns no results and user seems surprised
- User mentions they just installed the plugin

## Initial Setup

After installing the conversation-history plugin, users should index their existing conversations:

```bash
bash ${CLAUDE_PLUGIN_ROOT}/skills/getting-started/scripts/index-history.sh
```

Or they can run the command: `/conversation-history:index-history`

This scans `~/.claude/projects/` and indexes all existing conversation files into the database at `~/.claude/conversation-history.db`.

## How It Works

1. **Automatic indexing**: After setup, new conversations are automatically indexed via a Stop hook that fires when Claude finishes responding.

2. **Database location**: `~/.claude/conversation-history.db` - persists even after JSONL files are cleaned up.

3. **Incremental**: The indexer tracks what's already indexed and only processes new content.

## If Search Returns Nothing

If a user searches and gets no results:
1. Check if they've run the initial indexing
2. Suggest running `/conversation-history:index-history` to populate the database
3. Note that only conversations since the plugin was installed (or since running index-history) will be searchable

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dhughes) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
