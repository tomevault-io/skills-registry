---
name: intercom
description: Manage customer conversations, contacts, and help articles via Intercom API. Send messages and manage support inbox. Use when this capability is needed.
metadata:
  author: openclaw
---

# Intercom

Customer messaging platform.

## Environment

```bash
export INTERCOM_ACCESS_TOKEN="dG9rOxxxxxxxxxx"
```

## List Contacts

```bash
curl "https://api.intercom.io/contacts" \
  -H "Authorization: Bearer $INTERCOM_ACCESS_TOKEN" \
  -H "Accept: application/json"
```

## Search Contacts

```bash
curl -X POST "https://api.intercom.io/contacts/search" \
  -H "Authorization: Bearer $INTERCOM_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"query": {"field": "email", "operator": "=", "value": "user@example.com"}}'
```

## Create Contact

```bash
curl -X POST "https://api.intercom.io/contacts" \
  -H "Authorization: Bearer $INTERCOM_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"role": "user", "email": "user@example.com", "name": "John Doe"}'
```

## Send Message

```bash
curl -X POST "https://api.intercom.io/messages" \
  -H "Authorization: Bearer $INTERCOM_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "message_type": "inapp",
    "body": "Hey! How can I help?",
    "from": {"type": "admin", "id": "ADMIN_ID"},
    "to": {"type": "user", "id": "USER_ID"}
  }'
```

## List Conversations

```bash
curl "https://api.intercom.io/conversations" \
  -H "Authorization: Bearer $INTERCOM_ACCESS_TOKEN"
```

## Reply to Conversation

```bash
curl -X POST "https://api.intercom.io/conversations/{id}/reply" \
  -H "Authorization: Bearer $INTERCOM_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"message_type": "comment", "type": "admin", "admin_id": "ADMIN_ID", "body": "Thanks for reaching out!"}'
```

## Links
- Dashboard: https://app.intercom.com
- Docs: https://developers.intercom.com

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
