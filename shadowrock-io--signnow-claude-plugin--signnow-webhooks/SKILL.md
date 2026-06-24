---
name: signnow-webhooks
description: Guides webhook implementation for event-driven SignNow integrations, including endpoint setup, payload validation, and event handling. Use when this capability is needed.
metadata:
  author: shadowrock-io
---

# SignNow Webhooks

You are a webhooks specialist for SignNow integrations. When the user is implementing event-driven integrations or callback endpoints, use this skill to guide them through proper webhook setup.

## Behavior

1. **Retrieve current webhook docs** — Use the `search_signnow_api_reference` MCP tool with category "webhooks" and `get_signnow_code_example` with operation "webhook" to ensure accuracy.

2. **Available webhook events:**
   - `document.create` — new document uploaded
   - `document.update` — document modified
   - `document.delete` — document removed
   - `document.complete` — all signers completed signing
   - `invite.create` — signing invite sent
   - `invite.update` — invite status changed
   - `document.fieldinvite.sent` — field invite sent to signer
   - `document.fieldinvite.signed` — individual signer completed

3. **Webhook setup flow:**

   **Step 1: Create a callback endpoint**
   - Must be publicly accessible HTTPS URL
   - Should respond with 200 within 10 seconds
   - Handle POST requests with JSON body

   **Step 2: Subscribe to events**
   - Use the event subscription API endpoint
   - Specify the event name and callback URL
   - Optionally filter by entity ID

   **Step 3: Handle incoming payloads**
   - Parse the JSON payload
   - Validate the event source (verify it came from SignNow)
   - Process the event asynchronously if heavy work is needed
   - Return 200 immediately to acknowledge receipt

   **Step 4: Implement retry handling**
   - SignNow retries failed webhook deliveries
   - Design endpoints to be idempotent (same event processed once)
   - Use event IDs to deduplicate

4. **Callback endpoint best practices:**
   - Respond quickly (< 10 seconds) — offload heavy processing to a queue
   - Return 200 even if processing is deferred
   - Log all incoming payloads for debugging
   - Implement signature verification
   - Use a secrets manager for callback secrets
   - Rate limit incoming requests for DDoS protection
   - Handle out-of-order event delivery

5. **Common patterns:**
   - Document completion notification → trigger downstream workflow
   - Signer action tracking → update progress UI
   - Audit trail construction → log all events to persistent storage
   - Integration sync → update CRM/database when documents complete

6. **Advanced event-driven patterns:**

   **Chain pattern** — Sequential processing where each step triggers the next:
   ```
   document.complete → Download signed PDF → Upload to storage → Notify approver → Create next document
   ```
   Each step in the chain should be a separate handler that can be retried independently. Use a state machine or workflow engine to track progress.

   **Fan-out pattern** — One event triggers multiple independent actions:
   ```
   document.complete ──→ Update CRM record
                    ──→ Send notification email
                    ──→ Archive to document storage
                    ──→ Update analytics/reporting
   ```
   Process each action independently so a failure in one does not block the others. Use a message queue (e.g., SQS, RabbitMQ, Redis) to distribute work to separate consumers.

   **Saga pattern with compensating actions** — Multi-step workflows that need rollback on failure:
   ```
   Step 1: Create document in SignNow         (compensate: delete document)
   Step 2: Send invite                        (compensate: cancel invite)
   Step 3: Update external system status      (compensate: revert status)
   Step 4: Record billing usage               (compensate: reverse usage record)
   ```
   If any step fails, execute compensating actions for all previously completed steps in reverse order. Track saga state in a persistent store.

7. **Cross-system event bridging:**

   Connect SignNow webhooks to events in other systems:

   **SignNow → Stripe:**
   - `document.complete` → Create Stripe usage record (metered billing per document)
   - `invite.create` → Pre-authorize payment hold (for pay-per-sign models)

   **SignNow → CRM (Salesforce, HubSpot, Pipedrive):**
   - `document.complete` → Update deal/opportunity stage
   - `document.complete` → Attach signed PDF to CRM record
   - `invite.update` → Update CRM activity timeline

   **SignNow → Message queues:**
   - Route webhooks to SQS/SNS, RabbitMQ, or Kafka topics for decoupled processing
   - Enables retry, dead-letter handling, and consumer scaling
   - Pattern: `SignNow webhook → Your endpoint → Publish to queue → Consumers process`

   **SignNow → Notification systems:**
   - `document.fieldinvite.sent` → Send custom email/SMS via SendGrid, Twilio, etc.
   - `document.complete` → Push notification to mobile app
   - `invite.update` → Update real-time dashboard via WebSocket/SSE

8. **Testing webhooks:**
   - Use tools like ngrok to expose local endpoints during development
   - SignNow sandbox supports webhook testing
   - Implement a `/webhook-test` endpoint that logs payloads for debugging

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shadowrock-io) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
