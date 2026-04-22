---
name: messages-assistant
description: Send iMessages and search message history via Messages.app and chat.db. Use when this capability is needed.
metadata:
  author: spamsch
---

## Behavior Notes

### Sending Messages
- **Always confirm message content and recipient before sending.** Show the user exactly what will be sent.
- The `--to` parameter accepts phone numbers (with country code, e.g., +15551234567) or Apple ID emails.
- Messages are sent via iMessage by default. If the recipient is not on iMessage, delivery may fail silently.

### Reading Message History
- `list_imessage_chats` and `search_imessages` read directly from `~/Library/Messages/chat.db`.
- **chat.db is READ-ONLY** — never attempt to modify it. Modifying breaks iCloud sync.
- **Full Disk Access is required** to read chat.db. If access is denied, guide the user:
  "Grant Full Disk Access in System Settings > Privacy & Security > Full Disk Access for your terminal app."

### Search Strategy
- Use `search_imessages` with `--query` for text search and `--from` for sender filter.
- Narrow by `--days` (default 7) to keep queries fast.
- Combine `--query` and `--from` for precise results.

### Date Handling
- Messages uses Apple's CoreData epoch: nanoseconds since 2001-01-01.
- The scripts handle conversion automatically — dates are shown in local time.

### Common Request Patterns
- **"Send X a message"** → confirm content → `send_imessage(to="...", body="...")`
- **"Show recent chats"** → `list_imessage_chats(days=7)`
- **"What did X say about Y?"** → `search_imessages(query="Y", from="X")`
- **"Messages from last month"** → `search_imessages(days=30)`

### Limitations
- Cannot send MMS/SMS directly (iMessage only via AppleScript).
- Cannot read or send attachments via automation.
- Cannot delete or modify existing messages.
- Group chat names may appear as participant lists if no custom name is set.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spamsch) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
