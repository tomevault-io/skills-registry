---
name: fizzy-workflow
description: | Use when this capability is needed.
metadata:
  author: keskinonur
---

# Fizzy.do Guided Workflow Skill

Provides structured workflows for Fizzy.do task management. For direct operations, use the `fizzy-tasks` agent.

## Workflows

### Project Setup
**Triggers:** "set up Fizzy", "configure Fizzy for this project"

1. Check `.claude/fizzy_claude.json` for existing config
2. Verify FIZZY_TOKEN is available
3. List boards or create new one
4. Save configuration

### Sync Todos
**Triggers:** "sync my work", "sync todos to Fizzy"

1. Verify board is configured
2. Gather and summarize todos
3. Confirm card title
4. Execute `fizzy_sync_todos`

### Progress Review
**Triggers:** "review my progress", "show Fizzy status"

1. Load board config
2. Fetch and categorize cards
3. Present summary with step counts

### Session Cleanup
**Triggers:** "end of session", "wrap up"

1. Review current card from state
2. Confirm completion status
3. Close card if appropriate

## Configuration

| File | Contents |
|------|----------|
| `.claude/fizzy_claude.json` | `board_id`, `board_name` |
| `.claude/fizzy_state.json` | `card_number`, `card_title`, `step_map` |

## Related

- **Agent:** `fizzy-tasks` - Direct operations
- **Commands:** `/fizzy:configure`, `/fizzy:status`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/keskinonur) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
