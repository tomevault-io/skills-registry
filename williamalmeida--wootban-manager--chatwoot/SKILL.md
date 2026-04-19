---
name: chatwoot
description: Master the integration and management of Chatwoot. Includes conversation management, messaging, contact synchronization, and webhook configuration. Use when this capability is needed.
metadata:
  author: williamalmeida
---

# Chatwoot Skill

This skill provides comprehensive knowledge and patterns for working with **Chatwoot**, an open-source customer engagement platform.

## Core Concepts

### 1. Authentication
Chatwoot uses an `api_access_token` for authenticating Application APIs.
- **Header:** `api_access_token: YOUR_ACCESS_TOKEN`
- **Location:** Typically found in `Profile Settings -> Access Token`.

### 2. Basic Architecture
- **Account:** The root entity (e.g., `account_id: 1`).
- **Inbox:** Channels where messages arrive (Website, WhatsApp, Email).
- **Contact:** The end-user.
- **Conversation:** A session between an agent and a contact.
- **Message:** Individual entries within a conversation.

### 3. Key Endpoints (API v1)
Base URL: `https://chatwoot.domain.com/api/v1`

- **List Conversations:** `GET /accounts/{account_id}/conversations`
- **Create Contact:** `POST /accounts/{account_id}/contacts`
  - Body: `{ "name": "John Doe", "email": "john@example.com", "phone_number": "+123456" }`
- **Create Message:** `POST /accounts/{account_id}/conversations/{conversation_id}/messages`
  - Body: `{ "content": "Hello!", "message_type": "outgoing" }`
- **Search Contact:** `GET /accounts/{account_id}/contacts/search?q=phone_number`

### 4. Webhooks
Chatwoot notifies your system via POST requests.
- **Configure:** `Settings -> Integrations -> Webhooks`.
- **Common Events:**
  - `conversation_created`
  - `message_created` (Check `message_type` to distinguish between `incoming` and `outgoing`)
  - `contact_created`

## Code Example (Axios)

```typescript
import axios from 'axios';

const CHATWOOT_URL = 'https://app.chatwoot.com/api/v1';
const ACCESS_TOKEN = 'your_token_here';
const ACCOUNT_ID = '1';

const chatwoot = axios.create({
  baseURL: CHATWOOT_URL,
  headers: { 'api_access_token': ACCESS_TOKEN }
});

// Search for a contact by phone
const findContact = async (phoneNumber: string) => {
  const { data } = await chatwoot.get(`/accounts/${ACCOUNT_ID}/contacts/search`, {
    params: { q: phoneNumber }
  });
  return data.payload[0]; // Returns first match
};

// Send a message to a conversation
const sendMessage = async (conversationId: number, text: string) => {
  const { data } = await chatwoot.post(`/accounts/${ACCOUNT_ID}/conversations/${conversationId}/messages`, {
    content: text,
    message_type: 'outgoing'
  });
  return data;
};
```

## Best Practices
1. **Rate Limiting:** Chatwoot has rate limits. Implement retries for `429` errors.
2. **Private Notes:** Use `message_type: 'template'` or `is_private: true` for internal notes if supported by the endpoint.
3. **Labels:** Use labels (`POST /accounts/{account_id}/conversations/{id}/labels`) to categorize chats for Kanban or Funnels.
4. **Contact Sync:** Always search for an existing contact by email or phone before creating a new one to avoid duplicates.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/williamalmeida) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
