---
name: gogcli
description: Google Workspace CLI (gogcli) for Gmail, Calendar, Drive, Tasks, Contacts, Classroom, Chat. Use when accessing Google services via command line, automating email/calendar operations, managing Google Drive files, or working with Google Tasks. Use when this capability is needed.
metadata:
  author: kgsi
---

# gogcli - Google Workspace CLI

A command-line tool for interacting with Google Workspace services including Gmail, Calendar, Drive, Tasks, Contacts, Classroom, and Chat.

## Installation

```bash
# macOS (Homebrew)
brew install shinichi-kogiso/tap/gogcli

# Go install
go install github.com/shinichi-kogiso/gogcli@latest
```

## Authentication

gogcli uses OAuth 2.0 for authentication:

```bash
# Initial authentication (opens browser)
gog auth login

# Check current authentication status
gog auth status

# List authenticated accounts
gog auth list

# Switch between accounts
gog auth switch <email>

# Logout
gog auth logout
```

## Common Flags

These flags work across all commands:

| Flag | Description |
|------|-------------|
| `--json` | Output in JSON format |
| `--plain` | Output in plain text (no formatting) |
| `--account <email>` | Use specific Google account |
| `--limit <n>` | Limit number of results |
| `--page-token <token>` | Pagination token for next page |

## Available Services

- **Gmail** - Email operations (search, send, labels, drafts, attachments)
- **Calendar** - Calendar and event management
- **Drive** - File storage and sharing
- **Tasks** - Task list management
- **Contacts** - Contact management (People API)
- **Classroom** - Google Classroom operations
- **Chat** - Google Chat spaces and messages

## Quick Examples

```bash
# Search recent emails
gog gmail search "newer_than:1d"

# List today's calendar events
gog calendar events --time-min today --time-max tomorrow

# List Drive files
gog drive ls

# List tasks
gog tasks list

# Search contacts
gog contacts search "John"
```

## Service-Specific Documentation

See the following reference files for detailed command documentation:

- `GMAIL.md` - Gmail operations
- `CALENDAR.md` - Calendar operations
- `DRIVE.md` - Drive operations
- `TASKS.md` - Tasks operations
- `CONTACTS.md` - Contacts/People operations
- `CLASSROOM.md` - Classroom operations
- `CHAT.md` - Chat operations

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kgsi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
