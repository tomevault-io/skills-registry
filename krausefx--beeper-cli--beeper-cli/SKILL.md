---
name: beeper-cli
description: Read-only CLI for browsing, opening, and searching local Beeper chat history (SQLite-based) Use when this capability is needed.
metadata:
  author: KrauseFx
---

# Beeper CLI

Read-only access to local Beeper Desktop SQLite database for AI agents and automation.

## Features

- ✅ List threads/conversations
- ✅ Read messages with full metadata
- ✅ Full-text search (FTS5)
- ✅ Access to image/file attachments metadata
- ✅ JSON output for agent integration
- ✅ Contact name resolution from bridge databases

## Setup

**Built:** ✅ Pre-compiled binary at `skills/beeper-cli/beeper-cli`

**Requirements:**
- Go 1.22+ (for rebuilding only)
- Beeper Desktop installed
- SQLite database at: `~/Library/Application Support/BeeperTexts/index.db`

**Rebuild** (if needed):
```bash
cd skills/beeper-cli
go build -o beeper-cli ./cmd/beeper-cli
```

## Usage

### Wrapper script
```bash
cd skills/beeper-cli
./run.sh <command>
```

### List threads
```bash
./run.sh threads list --limit 50
./run.sh threads list --days 7
./run.sh threads list --json
```

### Read messages
```bash
./run.sh messages list --thread "!abc123:beeper.local" --limit 50
./run.sh messages list --thread "!abc123:beeper.local" --json
```

### Search messages
```bash
./run.sh search "christmas party" --limit 20
./run.sh search "party NEAR/5 christmas" --context 6
./run.sh search "invoice" --json
```

### Database info
```bash
./run.sh db info
```

## Accessing Images & Attachments

Messages with type `IMAGE`, `FILE`, `VIDEO`, etc. contain attachment metadata in the SQLite database.

**Direct SQLite query to get image URLs:**
```bash
sqlite3 ~/Library/Application\ Support/BeeperTexts/index.db \
  "SELECT message FROM mx_room_messages WHERE eventID = '\$EVENT_ID' LIMIT 1;"
```

The JSON `message` field contains:
- `attachments[].id` → mxc:// URL
- `attachments[].type` → img/file/video
- `attachments[].fileName` → Original filename
- `attachments[].mimeType` → MIME type
- `attachments[].fileSize` → Size in bytes
- `attachments[].size.width/height` → Image dimensions

**Note:** Images are encrypted. The `srcURL` contains encryption keys needed to decrypt via Beeper Desktop API.

## JSON Output Format

### Thread
```json
{
  "id": "!abc:beeper.local",
  "accountId": "whatsapp",
  "title": "Team Chat",
  "displayName": "Team Chat",
  "lastActivity": "2025-12-19T16:37:05+01:00",
  "isUnread": true,
  "unreadCount": 2,
  "totalMessages": 120
}
```

### Message
```json
{
  "id": 123,
  "eventId": "$abc",
  "threadId": "!abc:beeper.local",
  "senderId": "@alice:beeper.local",
  "senderName": "Alice",
  "timestamp": "2025-12-19T16:37:05+01:00",
  "isSentByMe": false,
  "type": "TEXT",
  "text": "See you at the party"
}
```

For images/files, `type` will be `IMAGE`, `FILE`, `VIDEO`, etc.

## When to Use

- Daily Beeper message analysis (read-only)
- Searching chat history across all platforms
- Extracting conversation metadata
- Identifying messages with attachments
- Building memory from past conversations

## Limitations

- **Read-only:** Cannot send messages
- **No image download:** Can see metadata but not decrypt images (requires Beeper Desktop API)
- **SQLite-based:** Requires Beeper Desktop to be installed (database must exist)

## Related

- For sending messages: Use the `message` tool with provider=whatsapp/telegram/signal
- For downloading decrypted images: Would need Beeper Desktop API integration (future enhancement)

---
> Source: [KrauseFx/beeper-cli](https://github.com/KrauseFx/beeper-cli) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-17 -->
