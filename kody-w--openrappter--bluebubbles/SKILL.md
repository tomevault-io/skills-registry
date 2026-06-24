---
name: bluebubbles
description: Send and receive iMessages through BlueBubbles server API. Use when this capability is needed.
metadata:
  author: kody-w
---

# BlueBubbles

Send and manage iMessages via the BlueBubbles server REST API.

## Requirements

- BlueBubbles server running on a Mac
- API password configured in openrappter config under `channels.bluebubbles`

## Send a Message

```bash
curl -X POST "http://BBSERVER:1234/api/v1/message/text" \
  -H "Content-Type: application/json" \
  -d '{"chatGuid": "iMessage;-;+1234567890", "message": "Hello!", "tempGuid": "'$(uuidgen)'"}'
```

## List Chats

```bash
curl "http://BBSERVER:1234/api/v1/chat?limit=10&offset=0&with=lastMessage"
```

## Get Messages

```bash
curl "http://BBSERVER:1234/api/v1/chat/CHAT_GUID/message?limit=25"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kody-w) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
