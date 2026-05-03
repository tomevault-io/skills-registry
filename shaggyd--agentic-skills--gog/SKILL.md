---
name: gog
description: Google Workspace CLI for Gmail, Calendar, Drive, Contacts, Sheets, and Docs Use when this capability is needed.
metadata:
  author: shaggyd
---

## What I do

Control Gmail, Calendar, Drive, Contacts, Sheets, and Docs from the command line using `gog`.

## Installation

```bash
brew install steipete/tap/gogcli
```

## Setup (one-time)

```bash
# Authenticate with OAuth
gog auth credentials /path/to/client_secret.json

# Add account with services
gog auth add you@gmail.com --services gmail,calendar,drive,contacts,sheets,docs

# List accounts
gog auth list
```

## Environment

```bash
# Set default account to avoid --account flag
export GOG_ACCOUNT=you@gmail.com
```

## Gmail

### Search emails

```bash
gog gmail search 'newer_than:7d' --max 10
gog gmail search 'from:boss@company.com is:unread' --max 20
```

### Send email

```bash
gog gmail send --to a@b.com --subject "Hi" --body "Hello"
```

## Calendar

### List events

```bash
gog calendar events <calendarId> --from 2024-01-01 --to 2024-01-31
```

## Drive

### Search files

```bash
gog drive search "query" --max 10
gog drive search "name contains 'report' and modifiedTime > '2024-01-01'"
```

## Contacts

```bash
gog contacts list --max 20
```

## Sheets

### Get values

```bash
gog sheets get <sheetId> "Tab!A1:D10" --json
```

### Update values

```bash
gog sheets update <sheetId> "Tab!A1:B2" --values-json '[["A","B"],["1","2"]]' --input USER_ENTERED
```

### Append rows

```bash
gog sheets append <sheetId> "Tab!A:C" --values-json '[["x","y","z"]]' --insert INSERT_ROWS
```

### Clear cells

```bash
gog sheets clear <sheetId> "Tab!A2:Z"
```

### Get metadata

```bash
gog sheets metadata <sheetId> --json
```

## Docs

### Export

```bash
gog docs export <docId> --format txt --out /tmp/doc.txt
```

### Cat (print contents)

```bash
gog docs cat <docId>
```

## Tips

- **Confirm before sending** emails or creating events
- Use `--json` for scripting-friendly output
- Use `--no-input` to skip confirmations in scripts
- Sheets values: use `--values-json` for complex data
- Docs: supports export/cat/copy only - in-place edits need Docs API client

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shaggyd) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
