---
name: checkpoint-patterns
description: LangGraph checkpointing patterns for state persistence with memory, SQLite, and Postgres backends. Use when implementing state recovery or human-in-the-loop workflows. Use when this capability is needed.
metadata:
  author: vanman2024
---

# Checkpoint Patterns

Skill for LangGraph state persistence and checkpointing.

## Overview

Persist state with:

- Memory checkpointer (dev)
- SQLite checkpointer
- Postgres checkpointer

## Use When

This skill is automatically invoked when:

- Adding state persistence
- Implementing human-in-the-loop
- Recovering from failures
- Managing thread state

## Available Templates

| Template                           | Description           |
| ---------------------------------- | --------------------- |
| `templates/memory_checkpoint.py`   | In-memory persistence |
| `templates/postgres_checkpoint.py` | Postgres persistence  |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vanman2024) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
