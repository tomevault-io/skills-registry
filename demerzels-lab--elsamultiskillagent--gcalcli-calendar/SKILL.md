---
name: gcalcli-calendar
description: Google Calendar skill for gcalcli: fast agenda (today/week/range), bounded keyword search, and safe event actions. Use when this capability is needed.
metadata:
  author: demerzels-lab
---

# gcalcli-calendar

Use `gcalcli` to read/search/manage Google Calendar from the command line: agenda, search, quick/add, edit, delete.

## Primary goal
Minimum tool calls + minimum output tokens, while staying correct.

## Hard rules (must follow)

### 1) Calendar scope is config-driven
Assume users configured gcalcli `config.toml`:
- `default-calendars` = what to include by default
- `ignore-calendars` = what to exclude (e.g., holidays)

Do not “search everything” by default. Only broaden scope if user explicitly asks “across all calendars” or results look obviously wrong.

### 2) Be concise: include scope only on empty results
- If you found events: output only the answer (events / next match).
- If you found nothing: include the searched window and calendar scope (default calendars + ignored calendars if known) and offer the smallest next step (expand window / search around a date).

### 3) Always bound keyword search by time
Default search window (unless user specifies otherwise):
- next **6 months** (~180 days) from today

If no matches in default window:
- say “No matches found in the next ~6 months (…); expand to 12 months or provide dates?”

### 4) Token-efficient output
Always add `--nocolor`.
Use default output for simple summarization.
Use `--tsv` only if you must reliably parse/dedupe/sort.

### 5) No invented explanations
If nothing is found, don’t guess why. State what you searched (only when empty) and propose the smallest next step.

### 6) Writes require confirmation
Before `quick/add/edit/delete`:
- summarize the exact change (calendar, title, date/time, duration, location, attendees if any)
- ask for explicit “yes”
- only then run the command

## Canonical commands (prefer these)

### Agenda (what’s on my calendar…)
Default agenda is already bounded (today → ~5 days). Use explicit ranges only when user asks.

- Today:
  - `gcalcli --nocolor agenda today tomorrow`
- Today + tomorrow:
  - `gcalcli --nocolor agenda today +2d`
- Next 7 days:
  - `gcalcli --nocolor agenda today +7d`
- Custom range:
  - `gcalcli --nocolor agenda <start> <end>`

### Keyword search (bounded)
- Default (next ~6 months):
  - `gcalcli --nocolor search "<query>" today +180d`
- Specific period:
  - `gcalcli --nocolor search "<query>" <start> <end>`

Behavior:
- “next occurrence” → return the earliest upcoming match
- “all matches” → return all matches in the window, sorted by start time

### Calendar list (only when scope needs debugging or user asks)
- `gcalcli --nocolor list`

### Week / month views
- `gcalcli --nocolor calw <weeks> [start]`
- `gcalcli --nocolor calm [start]`

### Create event (confirm first)
- Quick:
  - `gcalcli --nocolor --calendar "<CalendarName>" quick "<natural language event text>"`
- Detailed:
  - `gcalcli --nocolor --calendar "<CalendarName>" --title "<Title>" --where "<Location>" --when "<Start>" --duration <minutes> --description "<Text>" add`

### Edit / delete (confirm first)
- `gcalcli --nocolor --calendar "<CalendarName>" edit`
- `gcalcli --nocolor --calendar "<CalendarName>" delete`

## Response style (minimal)

If events found:
- concise list (group by day only when multiple days)
- omit scope

If no events found:
- “No events found in <from> → <to> (default calendars; ignored: …). Expand window or search around a date?”

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/demerzels-lab) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
