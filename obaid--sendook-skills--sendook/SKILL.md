---
name: sendook
description: Give AI agents their own email inboxes using the Sendook API. Use when building email agents, sending/receiving emails programmatically, managing inboxes, handling attachments, organizing threads, managing custom domains, or setting up real-time notifications via webhooks. Supports instant inbox creation and full email lifecycle management. Use when this capability is needed.
metadata:
  author: obaid
---

# Sendook Email SDK

Open-source email infrastructure for AI agents — instant inbox creation, sending/receiving, webhooks, custom domains, and more.

## Quick Start

```bash
npm install @sendook/node
```

```typescript
import Sendook from "@sendook/node";

const client = new Sendook(process.env.SENDOOK_API_KEY);

// Create an inbox
const inbox = await client.inbox.create({
  name: "support",
  email: "support@yourdomain.com",
});

// Send an email
await client.inbox.message.send({
  inboxId: inbox.id,
  to: ["user@example.com"],
  subject: "Welcome!",
  text: "Thanks for signing up.",
});
```

```bash
# Create an inbox
curl -X POST https://api.sendook.com/v1/inboxes \
  -H "Authorization: Bearer $SENDOOK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"name": "support", "email": "support@yourdomain.com"}'

# Send an email
curl -X POST https://api.sendook.com/v1/inboxes/{inbox_id}/messages/send \
  -H "Authorization: Bearer $SENDOOK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"to": ["user@example.com"], "subject": "Welcome!", "text": "Thanks for signing up."}'
```

## Resource Hierarchy

```
Organization
└── Inbox
    ├── Thread
    │   └── Message
    │       └── Attachment
    ├── Domain
    ├── Webhook
    └── API Key
```

- **Organization** — Top-level account, owns all resources
- **Inbox** — An email address that can send and receive messages
- **Thread** — A conversation grouping related messages
- **Message** — An individual email (sent or received)
- **Attachment** — A file attached to a message (base64 encoded)

## Inboxes

Create and manage email inboxes. Each inbox gets a unique email address.

### Create Inbox

```typescript
// Auto-generated address on default domain
const inbox = await client.inbox.create();

// Custom name and address
const inbox = await client.inbox.create({
  name: "notifications",
  email: "notifications@yourdomain.com",
});
```

```bash
curl -X POST https://api.sendook.com/v1/inboxes \
  -H "Authorization: Bearer $SENDOOK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"name": "notifications", "email": "notifications@yourdomain.com"}'
```

**Response** `201 Created`:
```json
{
  "id": "inbox_abc123",
  "name": "notifications",
  "domainId": "dom_xyz789",
  "email": "notifications@yourdomain.com",
  "createdAt": "2025-01-15T10:30:00Z"
}
```

### List Inboxes

```typescript
const inboxes = await client.inbox.list();
```

```bash
curl https://api.sendook.com/v1/inboxes \
  -H "Authorization: Bearer $SENDOOK_API_KEY"
```

### Get Inbox

```typescript
const inbox = await client.inbox.get("inbox_abc123");
```

```bash
curl https://api.sendook.com/v1/inboxes/inbox_abc123 \
  -H "Authorization: Bearer $SENDOOK_API_KEY"
```

### Delete Inbox

```typescript
await client.inbox.delete("inbox_abc123");
```

```bash
curl -X DELETE https://api.sendook.com/v1/inboxes/inbox_abc123 \
  -H "Authorization: Bearer $SENDOOK_API_KEY"
```

## Messages

Send, receive, and manage email messages.

### Send Message

```typescript
await client.inbox.message.send({
  inboxId: "inbox_abc123",
  to: ["recipient@example.com"],
  subject: "Hello from Sendook",
  text: "Plain text body",
  html: "<h1>Hello</h1><p>HTML body</p>",
});
```

```bash
curl -X POST https://api.sendook.com/v1/inboxes/inbox_abc123/messages/send \
  -H "Authorization: Bearer $SENDOOK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "to": ["recipient@example.com"],
    "subject": "Hello from Sendook",
    "text": "Plain text body",
    "html": "<h1>Hello</h1><p>HTML body</p>"
  }'
```

**Response** `202 Accepted` — message queued for delivery.

### Send with Attachments

```typescript
import { readFileSync } from "fs";

await client.inbox.message.send({
  inboxId: "inbox_abc123",
  to: ["recipient@example.com"],
  subject: "Report attached",
  text: "Please find the report attached.",
  attachments: [
    {
      content: readFileSync("report.pdf").toString("base64"),
      name: "report.pdf",
      contentType: "application/pdf",
    },
  ],
});
```

### Send with CC/BCC

```typescript
await client.inbox.message.send({
  inboxId: "inbox_abc123",
  to: ["primary@example.com"],
  cc: ["cc@example.com"],
  bcc: ["bcc@example.com"],
  subject: "Team update",
  text: "Here's the latest update.",
});
```

### Reply to Message

```typescript
await client.inbox.message.reply({
  inboxId: "inbox_abc123",
  messageId: "msg_def456",
  text: "Thanks for your email!",
  html: "<p>Thanks for your email!</p>",
});
```

```bash
curl -X POST https://api.sendook.com/v1/inboxes/inbox_abc123/messages/msg_def456/reply \
  -H "Authorization: Bearer $SENDOOK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"text": "Thanks for your email!"}'
```

### List Messages

```typescript
// List all messages
const messages = await client.inbox.message.list("inbox_abc123");

// Search messages (regex-based)
const results = await client.inbox.message.list("inbox_abc123", "invoice");
```

```bash
# List all messages
curl https://api.sendook.com/v1/inboxes/inbox_abc123/messages \
  -H "Authorization: Bearer $SENDOOK_API_KEY"

# Search messages
curl "https://api.sendook.com/v1/inboxes/inbox_abc123/messages?query=invoice" \
  -H "Authorization: Bearer $SENDOOK_API_KEY"
```

### Get Message

```typescript
const message = await client.inbox.message.get("inbox_abc123", "msg_def456");
```

```bash
curl https://api.sendook.com/v1/inboxes/inbox_abc123/messages/msg_def456 \
  -H "Authorization: Bearer $SENDOOK_API_KEY"
```

**Response**:
```json
{
  "id": "msg_def456",
  "from": "sender@example.com",
  "to": ["recipient@example.com"],
  "subject": "Hello",
  "text": "Plain text body",
  "html": "<p>HTML body</p>",
  "labels": [],
  "threadId": "thread_ghi789",
  "createdAt": "2025-01-15T10:35:00Z"
}
```

## Threads

Threads group related messages into conversations.

### List Threads

```typescript
const threads = await client.inbox.thread.list("inbox_abc123");
```

```bash
curl https://api.sendook.com/v1/inboxes/inbox_abc123/threads \
  -H "Authorization: Bearer $SENDOOK_API_KEY"
```

### Get Thread

```typescript
const thread = await client.inbox.thread.get("inbox_abc123", "thread_ghi789");
// thread.messages contains all messages in the conversation
```

```bash
curl https://api.sendook.com/v1/inboxes/inbox_abc123/threads/thread_ghi789 \
  -H "Authorization: Bearer $SENDOOK_API_KEY"
```

**Response**:
```json
{
  "id": "thread_ghi789",
  "subject": "Hello",
  "messages": [
    {
      "id": "msg_def456",
      "from": "sender@example.com",
      "to": ["recipient@example.com"],
      "subject": "Hello",
      "text": "Original message",
      "createdAt": "2025-01-15T10:35:00Z"
    },
    {
      "id": "msg_jkl012",
      "from": "recipient@example.com",
      "to": ["sender@example.com"],
      "subject": "Re: Hello",
      "text": "Reply message",
      "createdAt": "2025-01-15T10:40:00Z"
    }
  ]
}
```

## Domain Management

Register and verify custom sending domains for branded email addresses.

### Create Domain

```typescript
const domain = await client.domain.create({ name: "yourdomain.com" });
```

```bash
curl -X POST https://api.sendook.com/v1/domains \
  -H "Authorization: Bearer $SENDOOK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"name": "yourdomain.com"}'
```

### Get DNS Records

After creating a domain, retrieve the required DNS records to configure.

```typescript
const records = await client.domain.dns({ domainId: "dom_xyz789" });
// Returns MX, TXT (SPF/DKIM/DMARC), and CNAME records
```

```bash
curl https://api.sendook.com/v1/domains/dom_xyz789/dns \
  -H "Authorization: Bearer $SENDOOK_API_KEY"
```

**Response**:
```json
[
  { "type": "MX", "name": "yourdomain.com", "value": "mx.sendook.com" },
  { "type": "TXT", "name": "yourdomain.com", "value": "v=spf1 include:sendook.com ~all" },
  { "type": "CNAME", "name": "sendook._domainkey.yourdomain.com", "value": "dkim.sendook.com" }
]
```

### Verify Domain

After adding DNS records, verify the domain.

```typescript
await client.domain.verify({ domainId: "dom_xyz789" });
```

```bash
curl -X POST https://api.sendook.com/v1/domains/dom_xyz789/verify \
  -H "Authorization: Bearer $SENDOOK_API_KEY"
```

### Get Domain

```typescript
const domain = await client.domain.get({ domainId: "dom_xyz789" });
```

### Delete Domain

```typescript
await client.domain.delete({ domainId: "dom_xyz789" });
```

## Webhooks

Receive real-time notifications when email events occur. See [references/webhooks.md](references/webhooks.md) for detailed payload documentation.

### Create Webhook

```typescript
const webhook = await client.webhook.create({
  url: "https://your-app.com/webhooks/email",
  events: ["message.received", "message.delivered", "message.bounced"],
});
```

```bash
curl -X POST https://api.sendook.com/v1/webhooks \
  -H "Authorization: Bearer $SENDOOK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "url": "https://your-app.com/webhooks/email",
    "events": ["message.received", "message.delivered", "message.bounced"]
  }'
```

### Supported Events

| Event | Description |
|---|---|
| `inbox.created` | New inbox created |
| `inbox.deleted` | Inbox deleted |
| `inbox.updated` | Inbox settings changed |
| `message.received` | New email received in inbox |
| `message.sent` | Email sent from inbox |
| `message.delivered` | Email confirmed delivered |
| `message.bounced` | Email delivery failed |
| `message.complained` | Recipient marked email as spam |
| `message.rejected` | Email rejected by recipient server |

### List Webhooks

```typescript
const webhooks = await client.webhook.list();
```

### Get Webhook

```typescript
const webhook = await client.webhook.get("wh_mno345");
```

### Test Webhook

Send a test event to verify your endpoint is working.

```typescript
await client.webhook.test("wh_mno345");
```

```bash
curl -X POST https://api.sendook.com/v1/webhooks/wh_mno345/test \
  -H "Authorization: Bearer $SENDOOK_API_KEY"
```

### Delete Webhook

```typescript
await client.webhook.delete("wh_mno345");
```

### Express.js Webhook Handler

```typescript
import express from "express";

const app = express();
app.use(express.json());

app.post("/webhooks/email", (req, res) => {
  const { event, data } = req.body;

  switch (event) {
    case "message.received":
      console.log(`New email from ${data.from}: ${data.subject}`);
      break;
    case "message.bounced":
      console.log(`Bounce for ${data.to}: ${data.subject}`);
      break;
    case "message.complained":
      console.log(`Spam complaint from ${data.to}`);
      break;
  }

  // Always return 200 immediately
  res.status(200).send("OK");
});
```

## API Keys

Create and manage API keys for authentication.

### Create API Key

```typescript
const key = await client.apiKey.create({ name: "production" });
```

```bash
curl -X POST https://api.sendook.com/v1/api_keys \
  -H "Authorization: Bearer $SENDOOK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"name": "production"}'
```

### List API Keys

```typescript
const keys = await client.apiKey.list();
```

### Get API Key

```typescript
const key = await client.apiKey.get({ apiKeyId: "key_pqr678" });
```

### Delete API Key

```typescript
await client.apiKey.delete({ apiKeyId: "key_pqr678" });
```

## Error Handling

```typescript
try {
  await client.inbox.message.send({
    inboxId: "inbox_abc123",
    to: ["recipient@example.com"],
    subject: "Hello",
    text: "Body",
  });
} catch (error) {
  if (error.response) {
    // Server responded with error
    console.error(error.response.status, error.response.data);
  } else if (error.request) {
    // No response received
    console.error("No response:", error.request);
  } else {
    // Request setup failure
    console.error("Error:", error.message);
  }
}
```

### Common Status Codes

| Status | Meaning |
|---|---|
| `200` | Success |
| `201` | Resource created |
| `202` | Accepted (async operations like send) |
| `400` | Bad request — invalid parameters |
| `401` | Unauthorized — invalid or missing API key |
| `404` | Resource not found |
| `429` | Rate limit exceeded |
| `500` | Internal server error |

**Error response shape**:
```json
{
  "message": "Inbox not found",
  "code": "not_found",
  "details": {}
}
```

## Best Practices

- **Deliverability**: Always configure SPF, DKIM, and DMARC records for custom domains. Use `client.domain.dns()` to get the required records, then verify with `client.domain.verify()`.
- **Webhooks**: Return `200` immediately from webhook handlers and process events asynchronously. Use a message queue for heavy processing.
- **Attachments**: Base64 encoding increases file size by ~33%. Keep attachments under 10MB.
- **Rate Limits**: The API allows ~1,000 requests/minute and ~50,000 requests/day. Implement exponential backoff on `429` responses.
- **Search**: Use `client.inbox.message.list(inboxId, query)` with regex patterns to search across to/from/cc, subject, and body fields.
- **Threading**: Use `client.inbox.thread.get()` to retrieve full conversation history instead of fetching individual messages.

## API Reference

| Method | Description |
|---|---|
| `client.inbox.create(options?)` | Create a new inbox |
| `client.inbox.list()` | List all inboxes |
| `client.inbox.get(inboxId)` | Get inbox details |
| `client.inbox.delete(inboxId)` | Delete an inbox |
| `client.inbox.message.send(options)` | Send an email |
| `client.inbox.message.reply(options)` | Reply to a message |
| `client.inbox.message.list(inboxId, query?)` | List/search messages |
| `client.inbox.message.get(inboxId, messageId)` | Get a message |
| `client.inbox.thread.list(inboxId)` | List threads |
| `client.inbox.thread.get(inboxId, threadId)` | Get thread with messages |
| `client.domain.create(options)` | Register a custom domain |
| `client.domain.get(options)` | Get domain details |
| `client.domain.dns(options)` | Get required DNS records |
| `client.domain.verify(options)` | Verify domain DNS configuration |
| `client.domain.delete(options)` | Delete a domain |
| `client.webhook.create(options)` | Create a webhook |
| `client.webhook.list()` | List all webhooks |
| `client.webhook.get(webhookId)` | Get webhook details |
| `client.webhook.test(webhookId)` | Send test event to webhook |
| `client.webhook.delete(webhookId)` | Delete a webhook |
| `client.apiKey.create(options)` | Create an API key |
| `client.apiKey.list()` | List all API keys |
| `client.apiKey.get(options)` | Get API key details |
| `client.apiKey.delete(options)` | Delete an API key |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/obaid) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
