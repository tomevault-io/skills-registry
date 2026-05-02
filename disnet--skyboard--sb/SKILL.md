---
name: sb
description: | Use when this capability is needed.
metadata:
  author: disnet
---

# Skyboard CLI (`sb`)

Skyboard is a collaborative kanban board built on the AT Protocol. The `sb` CLI lets you manage boards, columns, and cards from the terminal.

## Prerequisites

- `sb` is installed globally (`npm i -g skyboard-cli`)
- User is logged in (`sb login <handle>`)
- A default board is set (`sb use <board>`) or `--board` is passed on each command

## Key Concepts

- **Boards** — kanban boards with named columns. Referenced by name, rkey, AT URI, or web URL. All board-scoped commands accept `--board <ref>` to target a specific board without relying on `sb use`.
- **Board ref formats** — `--board` accepts: AT URI (`at://did:plc:xxx/dev.skyboard.board/rkey`), DID:rkey pair (`did:plc:xxx:rkey`), or a board name substring.
- **Columns** — matched by exact name (case-insensitive), name prefix, substring, or 1-based index from `sb cols`.
- **Cards** — tasks on the board. Referenced by TID rkey prefix (minimum 4 characters, like git short hashes). Use `sb cards` to see short refs.
- **Short refs** — first 7 characters of a card's rkey, displayed by `sb cards` and `sb show`. Provide at least 4 characters to identify a card.

## Workflow

1. Check auth: `sb whoami --json`
2. List boards: `sb boards --json`
3. Set default board: `sb use <board>` (or pass `--board <ref>` on each command)
4. View columns: `sb cols --json`
5. List cards: `sb cards --json`
6. Create/edit/move/comment/delete cards as needed

## Important Rules

- **Always use `--json`** on commands that support it so you get structured output. Present results as human-friendly summaries.
- **Commands supporting `--json`**: whoami, status, boards, cols, cards, new, show, mv, edit, comment, rm
- **Commands without `--json`**: login, logout, use, add
- **Use `--board`** when operating on a specific board in automated/scripted contexts (e.g., ralph mode) to avoid conflicts with the global default. All board-scoped commands (cols, cards, new, show, mv, edit, comment, rm) support `--board <ref>`.
- For `sb rm`, always pass `--force` to skip the interactive prompt, but **ask the user for confirmation first** in conversation.
- If a command needs a board and none is set, run `sb boards --json` to show options and ask the user which to use.
- If a card ref is ambiguous, the CLI will list candidates — present them to the user and ask which one.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/disnet) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
