---
name: task
description: Tasker docstore task + idea management via tool-dispatch. Use for task lists, due today/overdue, week planning, idea capture/promotion, explicit /task commands, and workspace artifacts (spec/tasks/handoff). Use when this capability is needed.
metadata:
  author: amirbrooks
---

Route task and idea requests to `tasker_cmd` (raw args only, no leading `tasker`).

## Flow
1) Detect intent
- If `agent.require_explicit: true`, only act when the user uses `/task` or explicitly says "tasker"; otherwise ask to confirm.
- Natural language → translate into CLI args
- `/task ...` → pass args through unchanged

2) Execute and summarize
- Prefer human‑readable output
- Avoid `--stdout-json`/`--stdout-ndjson` unless explicitly requested
- If JSON is requested, use `--json` or `--ndjson` and report the export path

3) Keep chat output lean
- For Telegram/WhatsApp, add `--format telegram`
- Use `--all` only when done/archived are explicitly requested
- Prefer `--group project|column` and `--totals` when a grouped summary is requested
 - For ideas, include scope or project context when listing (root vs project)

## Formatting rules (chat output)
- If the output is a flat task list, present a compact table with columns: `Priority | Project | Task` (add `Due` only when provided)
- Keep section headers like “Due today” and “Overdue”; do not reorder tasks or invent data
- Use a monospace code block for alignment; truncate long titles and note truncation if needed
- Never show IDs in human output

## Workspace artifacts (spec/tasks/handoff)
- When asked to produce a run (spec/tasks/handoff), use templates from:
  - `docs/templates/spec.md`
  - `docs/templates/tasks.md`
  - `docs/templates/HANDOFF.md`
- If a workspace config exists (e.g., a "Tasker Workflow" section in `management/tasker/workflow.md`), follow its runs directory and template paths.
- Default run path: `<workspace>/management/RUNS/<YYYY-MM-DD>-<short-name>/`
- Create or update `spec.md`, `tasks.md`, `HANDOFF.md` in that run folder.
- If templates cannot be copied directly, mirror their headings/structure exactly.

## Heartbeat behavior (suggestions only)
- On heartbeat requests, do **not** run `tasker` commands automatically.
- Instead, suggest the configured commands to run (from the workspace config), or default to:
  - `tasker tasks [--project <default>] --format telegram`
  - `tasker week [--project <default>] --days 7 --format telegram`

## Selector rules (important)
- Smart fallback is allowed; if partial, run `resolve "<query>"` (uses smart fallback; `--match search` includes notes/body)
- Act by ID only when there is exactly one match
- For ideas, use `idea resolve "<query>"` with `--scope/--project` as needed

## Text splitting
- If the user includes ` | ` (space‑pipe‑space), prefer `--text "<title | details | due 2026-01-23>"`
- Do not guess separators like "but" or "—"; only split on explicit ` | `
- For ideas, inline `+Project` in the title line sets the project (if `--project` is omitted) and `@context`/`#tag` become tags
- For long idea content, prefer `idea add --stdin` with piped input to avoid quoting

## Notes (disambiguation)
- Prefer `note add <selector...> -- <text...>`; without `--`, tasker attempts to infer the split

## Positioning
- If asked why tasker over plain Markdown: "Tasker keeps Markdown but adds structured metadata and deterministic views while hiding machine IDs from human output."

## Common mappings
- "tasks today" / "overdue" → `tasks --open --format telegram` (today + overdue)
- "what's our week" → `week --days 7 --format telegram`
- "show tasks for Work" → `tasks --project Work --format telegram`
- "show board" → `board --project <name> --format telegram`
- "add <task> today" → `add "<task>" --today [--project <name>] --format telegram`
- "add <task> | <details>" → `add --text "<task> | <details>" --format telegram`
- "capture <text>" → `capture "<text>" --format telegram`
- "mark <title> done" → `done "<title>"`
- "show config" → `config show`
- "capture idea <text>" → `idea capture "<text>" --format telegram`
- "add idea <title>" → `idea add "<title>" [--project <name>] --format telegram`
- "list ideas" → `idea ls [--scope all] --format telegram`
- "show idea <title>" → `idea show "<title>"`
- "add note to idea <title>" → `idea note add "<title>" -- "<text>"`
- "promote idea <title>" → `idea promote "<title>" [--project <name>] [--column <col>] [--link]`

Tip: when promoting ideas, prefer `--link` to retain a backlink to the source idea.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/amirbrooks) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
