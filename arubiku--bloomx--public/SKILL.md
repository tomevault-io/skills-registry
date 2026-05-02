---
name: bloomx-mail
description: Full email management (send, receive) via BloomX. Requires authentication. Use when this capability is needed.
metadata:
  author: arubiku
---

# BloomX Mail Service

Manage your emails directly through BloomX.

## Authentication

This skill requires specific authentication handling.
1.  **Connect**: POST to `/api/molt/auth/connect` with `{ email, password }` to get `accessToken` and `refreshToken`.
2.  **Use**: Send `Authorization: Bearer <accessToken>` in headers.
3.  **Refresh**: POST to `/api/molt/auth/refresh` with `{ refreshToken }` when 401.

## Tools

### send_email

Send an email to a recipient.

**Method**: `POST`
**URL**: `/api/molt/messages`
**Content-Type**: `application/json`

**Body**:
```json
{
  "to": "user@example.com",
  "subject": "Subject",
  "text": "Body content",
  "cc": ["manager@example.com"],
  "bcc": ["archive@example.com"],
  "reply_to": "support@example.com",
  "scheduled_at": "2023-10-25T12:00:00.000Z"
}
```

| Parameter | Type | Description |
| :--- | :--- | :--- |
| `to` | string/array | Recipient email(s) |
| `subject` | string | Email subject |
| `text` | string | Plain text content |
| `html` | string | HTML content (optional) |
| `cc` | string/array | CC recipients (optional) |
| `bcc` | string/array | BCC recipients (optional) |
| `reply_to` | string | Reply-To address (optional) |
| `scheduled_at` | string | ISO 8601 date to schedule sending (optional) |

### get_recent_emails

Get a list of recent emails from your inbox or specific folder.

**Method**: `GET`
**URL**: `/api/molt/messages`
**Query Parameters**:
- `limit` (optional): Number of emails to retrieve (default: 10).
- `folder` (optional): Folder to retrieve from (default: "inbox").

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/arubiku) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
