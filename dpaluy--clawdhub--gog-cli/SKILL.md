---
name: gog-cli
description: Google Workspace CLI for Gmail, Calendar, Drive, Docs, Sheets, Tasks, and more Use when this capability is needed.
metadata:
  author: dpaluy
---

# gog-cli Skill

Fast, script-friendly CLI for Google Workspace services: Gmail, Calendar, Drive, Docs, Sheets, Slides, Chat, Classroom, Contacts, Tasks, People, Groups, and Keep.

## Prerequisites

```bash
# Install
brew install steipete/tap/gogcli

# Authenticate (requires OAuth credentials from Google Cloud Console)
gog auth credentials <path/to/client_secret.json>
gog auth add user@gmail.com --services all
```

## Core Principles

1. **Always use `--json`** for parsing command output - human-readable output is for display only
2. **Verify before destructive operations** - list/search before delete/modify
3. **Use `--force`** only when explicitly requested by user
4. **Check account context** with `gog auth status` when working across multiple accounts

## Quick Reference

| Service | List | Get | Create | Search |
|---------|------|-----|--------|--------|
| Gmail | `gog gmail search "..."` | `gog gmail thread get <id>` | `gog gmail send ...` | Same as list |
| Calendar | `gog calendar events <cal>` | `gog calendar event <cal> <id>` | `gog calendar create <cal>` | `--query` flag |
| Drive | `gog drive ls` | `gog drive get <id>` | `gog drive upload <file>` | `gog drive search "..."` |
| Tasks | `gog tasks list <listId>` | `gog tasks get <list> <id>` | `gog tasks add <list>` | N/A |
| Contacts | `gog contacts list` | `gog contacts get <id>` | `gog contacts create` | `gog contacts search "..."` |

## Common Workflows

### Email Management

```bash
# Search unread emails
gog gmail search "is:unread" --json

# Read specific thread
gog gmail thread get <threadId> --json

# Send email with attachment
gog gmail send --to user@example.com --subject "Subject" --body "Message" --attach file.pdf

# Reply to thread
gog gmail send --to user@example.com --subject "Re: Subject" --body "Reply" --reply-to-message-id <msgId>

# Apply label
gog gmail thread modify <threadId> --add "Label Name"
```

### Calendar Operations

```bash
# List today's events
gog calendar events primary --from "$(date +%Y-%m-%d)T00:00:00" --to "$(date +%Y-%m-%d)T23:59:59" --json

# Create event with attendees
gog calendar create primary --summary "Meeting" --from "2024-12-20T14:00:00" --to "2024-12-20T15:00:00" --attendees "user@example.com"

# Check availability
gog calendar freebusy "user1@example.com,user2@example.com" --from <start> --to <end> --json

# Accept invitation
gog calendar respond primary <eventId> --status accepted
```

### File Operations

```bash
# Search files
gog drive search "quarterly report" --json

# Download file
gog drive download <fileId> --out ~/Downloads/

# Upload to folder
gog drive upload file.pdf --parent <folderId>

# Share file
gog drive share <fileId> --email user@example.com --role writer
```

### Spreadsheet Operations

```bash
# Read range
gog sheets read <spreadsheetId> "Sheet1!A1:D10" --json

# Write data (JSON array of arrays)
gog sheets write <spreadsheetId> "Sheet1!A1" --values '[["Name","Value"],["Alice",100]]'

# Append rows
gog sheets append <spreadsheetId> "Sheet1!A:B" --values '[["New","Row"]]'
```

## Output Modes

| Flag | Format | Use Case |
|------|--------|----------|
| (none) | Human-readable | Display to user |
| `--json` | JSON | Parsing, scripting |
| `--plain` | TSV | Shell piping |

## Multi-Account Usage

```bash
# Specify account per command
gog --account work@example.com gmail search "is:unread"

# Set default via environment
export GOG_ACCOUNT=work@example.com

# Use aliases
gog auth alias set work work@example.com
gog --account work gmail search "is:unread"
```

## Error Recovery

| Error | Solution |
|-------|----------|
| Token expired | `gog auth add user@gmail.com --force-consent` |
| Wrong scopes | Re-add with required services: `--services gmail,calendar,drive` |
| Rate limited | Wait and retry; use `--max` to limit batch sizes |

## Safety Guidelines

**Destructive operations requiring confirmation:**
- `gog drive delete` - moves to trash
- `gog calendar delete` - removes event
- `gog gmail filters delete` - removes filter rule

**Prefer these safe patterns:**
1. Search/list before modifying: `gog gmail search "..." --json` then modify
2. Use labels over delete: `gog gmail thread modify <id> --add Archive --remove INBOX`
3. Download before deleting: `gog drive download <id>` then `gog drive delete <id>`

## References

- [Command Reference](references/command-reference.md) - Complete command syntax
- [Gmail Operations](references/gmail.md) - Search syntax, sending, labels
- [Calendar Operations](references/calendar.md) - Events, availability, recurrence
- [Drive & Docs](references/drive-docs.md) - Files, Sheets, export
- [Authentication](references/authentication.md) - Multi-account, service accounts
- [Configuration](references/configuration.md) - Environment variables, output formats
- [Other Services](references/other-services.md) - Tasks, Contacts, Chat, Classroom

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dpaluy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
