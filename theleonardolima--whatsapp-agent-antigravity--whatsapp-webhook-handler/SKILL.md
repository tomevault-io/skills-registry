---
name: whatsapp-webhook-handler
description: Setup and process WhatsApp webhooks. Use for tasks like "How do I verify my webhook?" or "Parse this incoming message notification". Use when this capability is needed.
metadata:
  author: theleonardolima
---

# Whatsapp Webhook Handler

## Goal
To successfully receive, verify, and process real-time notifications from the WhatsApp Business Platform.

## Capabilities
1.  **Verification**: Implement the `hub.challenge` handshake for endpoint setup.
2.  **Security**: Validate the `X-Hub-Signature-256` HMAC-SHA256 signature.
3.  **Payload Parsing**: Extract data from incoming messages (text, media, interactive) and status updates.
4.  **Error Handling**: Process account alerts and template status changes.

## Workflow

### 1. Verification Handshake (GET)
When configuring the webhook in Meta dashboard, the server must handle:
- **Params**: `hub.mode`, `hub.verify_token`, `hub.challenge`.
- **Response**: Return the `hub.challenge` as the response body with status 200.

### 2. Event Processing (POST)
**Security Check**:
Generate `sha256` of raw body using your App Secret. Compare with `X-Hub-Signature-256` header (prefixed with `sha256=`).

**Parsing Hierarchy**:
1. Check `entry[].changes[].value.messages[]` for user input.
2. Check `entry[].changes[].value.statuses[]` for delivery/pricing info.
3. Check `entry[].changes[].value` for `account_update` or `template_status_update`.

### 3. Response Requirements
- ALWAYS return `200 OK` promptly to acknowledge receipt. 
- Process long-running logic asynchronously to avoid timeout (Meta retries for 7 days if you fail).

## Constraints
- **Batching**: Payload can contain multiple `entry` and `changes` items.
- **Media**: For media webhooks, extract the `id` and use the `/media` endpoint to download the file (URLs expire in 5m).

## Reference
- **Payload Examples**: See `references/webhook_examples.md` for JSON structures of Text, Interactive, and Status events.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/theleonardolima) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
