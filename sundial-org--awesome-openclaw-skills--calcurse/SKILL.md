---
name: calcurse
description: Use when working with a text-based calendar and scheduling application. Use strictly for CLI-based calendar management.
metadata:
  author: sundial-org
---

# calcurse

A text-based calendar and scheduling application.

## Usage (CLI Mode)

Use `calcurse` in non-interactive mode for quick queries and updates.

### Query
List appointments for the next 2 days:
```bash
calcurse -r2
```

Query a specific date range:
```bash
calcurse -Q --from 2026-01-20 --to 2026-01-22
```

### Add Items
Add an appointment:
```bash
calcurse -a "Meeting with Team" 2026-01-21 14:00 60
```
(Format: Description, Date, Time, Duration in mins)

Add a todo:
```bash
calcurse -t "Buy milk" 1
```
(Format: Description, Priority)

## Interactive Mode (TUI)
For the full TUI experience, run in a PTY session (e.g., inside `tmux` or using `process` with `pty=true`).
```bash
calcurse
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sundial-org) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
