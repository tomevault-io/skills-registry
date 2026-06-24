---
name: y-search-codex
description: Search Codex session history stored in JSONL logs with ripgrep. Use when you need to find earlier user messages, assistant responses, tool calls, file edits, commands, or decisions from previous Codex sessions, especially when a session filename is provided. Use when this capability is needed.
metadata:
  author: eqtylab
---

## Inputs

- Accept a session file name or full path from the user.
- Default session root to `~/.codex/sessions` when only a file name is provided.
- Confirm the resolved file exists before running search commands.

## Search Strategy

Each event is a single long JSON line. Avoid raw full-line searches that flood output.

1. Count matches first.
2. Pull short snippets around matches.
3. Narrow snippets with a second filter.
4. Open full context only after locating a precise target.

Always use at least one output limiter:

- `-c` for counts
- `-o '.{0,60}pattern.{0,60}'` for snippets
- `-M 200` or `-M 500` to truncate long lines
- `| head -20` to cap result count

## Core Commands

```bash
# 1) Count first
rg -c 'search_term' ~/.codex/sessions/<session-file>.jsonl

# 2) Snippets with local context
rg -o '.{0,60}search_term.{0,60}' ~/.codex/sessions/<session-file>.jsonl | head -20

# 3) Narrow further
rg -o '.{0,60}search_term.{0,60}' ~/.codex/sessions/<session-file>.jsonl | rg 'new_string'

# 4) Full context only when needed
rg '"name":"Edit".*search_term' ~/.codex/sessions/<session-file>.jsonl -M 500
```

Never run:

```bash
rg 'pattern' ~/.codex/sessions/<session-file>.jsonl
```

## Useful Patterns

```bash
# Human user inputs
rg -o '.{0,40}"type":"user".{0,80}"userType":"external".{0,40}' <session.jsonl> | head -10

# Tool edits
rg -c '"name":"Edit"' <session.jsonl>
rg -o '.{0,60}"name":"Edit".{0,60}' <session.jsonl> | head -10

# Commands run by shell tools
rg -o '.{0,100}"command":"[^"]*".{0,40}' <session.jsonl> | head -20

# Mentions tied to file edits
rg -o '.{0,60}auth.{0,60}' <session.jsonl> | rg 'file_path'
```

## JSONL Reference

Use these keys when filtering:

- Message roles: `"type":"user"`, `"type":"assistant"`, `"type":"tool_result"`
- Human messages: `"userType":"external"`
- Tool blocks: `"type":"tool_use"`
- Common tool names: `"name":"Edit"`, `"name":"Write"`, `"name":"Bash"`, `"name":"Task"`
- Useful fields: `"timestamp"`, `"agentId"`, `"input"`

## Workflow

1. Resolve session path from user input (`<name>.jsonl` or full path).
2. Ask for a target term if none is provided.
3. Run count, then snippets, then narrowed snippets.
4. Run full-context grep only for promising matches.
5. Report concise findings with quoted snippets and exact command(s) used.

## Safety

- Keep searches read-only.
- Redact secrets if surfaced in snippets.
- Prefer targeted patterns over broad regex to reduce accidental disclosure.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eqtylab) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
