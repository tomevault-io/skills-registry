---
name: autosend-nodejs
description: Use when you need to send transactional or marketing emails, manage contacts, or bulk email users from a Node.js application using the AutoSend Node.js SDK.
metadata:
  author: shreyap2702
---

# AutoSend (Node.js SDK)

This Skill enables sending emails and managing contacts using the official AutoSend Node.js SDK (`autosendjs`).  
Use it for transactional emails (welcome, verification, password reset), marketing emails, bulk sends, and syncing user data to AutoSend Contacts.

---

## When to use this Skill

Use this Skill **when all of the following are true**:
- The environment is **Node.js**
- Email sending or contact management is required
- AutoSend is the email provider
- The AutoSend Node.js SDK (`autosendjs`) is available

Do **not** use this Skill for:
- SMTP-only integrations
- Frontend/browser code
- Non-Node.js runtimes
- Providers other than AutoSend

---

## Prerequisites (verify before sending)

Before attempting to send emails, verify that:
1. A **verified sending domain** exists in AutoSend
2. A valid **AutoSend API key** is available (prefer `process.env.AUTOSEND_API_KEY`)

---

## SDK Initialization

Always initialize the SDK once and reuse the instance.

```typescript 
import { Autosend } from 'autosendjs';

const autosend = new Autosend(process.env.AUTOSEND_API_KEY);
```

Optional configuration:

```typescript
const autosend = new Autosend(process.env.AUTOSEND_API_KEY, {
  timeout: 30000,
  maxRetries: 3,
  debug: false,
});
```

---

## Sending Emails

Plain text email
```typescript
await autosend.emails.send({
  from: { email: 'hello@yourdomain.com' },
  to: { email: 'user@example.com' },
  subject: 'Hello',
  text: 'Welcome to AutoSend!',
});
```

HTML email
```typescript
await autosend.emails.send({
  from: { email: 'hello@yourdomain.com', name: 'Your Company' },
  to: { email: 'user@example.com', name: 'John Doe' },
  subject: 'Welcome',
  html: '<h1>Welcome!</h1><p>Thanks for signing up.</p>',
});
```
--- 
## Using Templates

Use templates when emails are reused or personalized at scale.

```typescript
await autosend.emails.send({
  from: { email: 'hello@yourdomain.com' },
  to: { email: 'user@example.com' },
  subject: 'Order Confirmation',
  templateId: 'template_id',
  dynamicData: {
    name: 'John',
    orderNumber: '12345',
  },
});
```

--- 
## Bulk Emails

Prefer bulk sending when emailing multiple recipients.

```typescript
await autosend.emails.bulk({
  emails: [
    {
      from: { email: 'hello@yourdomain.com' },
      to: { email: 'user1@example.com' },
      subject: 'Hello',
      html: '<p>Welcome!</p>',
    },
    {
      from: { email: 'hello@yourdomain.com' },
      to: { email: 'user2@example.com' },
      subject: 'Hello',
      html: '<p>Welcome!</p>',
    },
  ],
});
```
---

## Contact Management

### Create a contact

```typescript
await autosend.contacts.create({
  email: 'user@example.com',
  firstName: 'John',
  lastName: 'Doe',
  listIds: ['list_id'],
  customFields: {
    plan: 'pro',
  },
});
```

### Upsert a contact (recommended for sync)

```typescript
await autosend.contacts.upsert({
  email: 'user@example.com',
  firstName: 'Jane',
  customFields: {
    plan: 'enterprise',
  },
});
```

### Get a contact

```typescript
const contact = await autosend.contacts.get('contact_id');
```

### Delete a contact

```typescript
await autosend.contacts.delete('contact_id');
```
---
## Resend Compatibility Adapter

Use this only when migrating from Resend-style APIs.

```typescript
import { Resend } from 'autosendjs/resend';

const resend = new Resend(process.env.AUTOSEND_API_KEY);
```

Notes:
Uses properties instead of customFields
Uses remove() instead of delete()

--- 

## Error Handling

Wrap all SDK calls in try/catch and handle by status code:

```typescript
try {
  await autosend.emails.send({
    from: { email: 'hello@yourdomain.com' },
    to: { email: 'user@example.com' },
    subject: 'Hello',
    html: 'Welcome!',
  });
} catch (error) {
  switch (error.status) {
    case 401:
      // Invalid or revoked API key — verify in AutoSend dashboard
      console.error('Authentication failed. Check your API key.');
      break;
    case 400:
      // Bad request — likely an unverified domain or malformed payload
      console.error('Bad request:', error.message);
      break;
    case 429:
      // Rate limited — back off before retrying, or switch to bulk sending
      console.error('Rate limit hit. Retry after backoff.');
      break;
    case 408:
      // Timeout — increase timeout in SDK config or reduce payload size
      console.error('Request timed out.');
      break;
    default:
      console.error('Unexpected error:', error.message);
      throw error;
  }
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shreyap2702) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
