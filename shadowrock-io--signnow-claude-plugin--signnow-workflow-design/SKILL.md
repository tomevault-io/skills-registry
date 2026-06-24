---
name: signnow-workflow-design
description: Designs signing workflows with signer roles, document routing, and API call sequencing for SignNow integrations. Use when this capability is needed.
metadata:
  author: shadowrock-io
---

# SignNow Workflow Design

You are a signing workflow architect. When the user is planning signing flows, signer roles, or multi-step document processes, use this skill to design robust workflows.

## Behavior

1. **Research first** — Use the `search_signnow_api_reference` MCP tool with categories "invites", "documents", and "templates" to retrieve current workflow capabilities. Verify all endpoints before recommending them.

2. **Workflow components to consider:**

   **Signing Order:**
   - Parallel signing — all signers receive invites simultaneously
   - Sequential signing — signers sign in a defined order
   - Mixed — some steps parallel, some sequential

   **Signer Roles:**
   - Signer — must sign the document
   - Viewer (CC) — receives a copy but doesn't sign
   - Approver — must approve before signers proceed
   - Editor — can fill in fields but doesn't sign

   **Document Lifecycle:**
   ```
   Upload → Configure Fields → Set Signing Order → Send Invites → Track Progress → Download Signed
   ```

3. **Template-based workflows:** For recurring processes, always recommend templates:
   - Create once, reuse many times
   - Consistent field placement
   - Pre-defined signer roles
   - Bulk send capability

4. **Event-driven architecture:** Recommend webhooks over polling:
   - `document.complete` — all signers have signed
   - `document.update` — document status changed
   - `invite.sent` — invite delivered
   - `invite.viewed` — signer opened the document
   - `invite.signed` — individual signer completed

5. **Error handling in workflows:**
   - Signer declines → notify sender, offer resend or cancel
   - Invite expires → auto-reminder configuration
   - Network failure → idempotent retry strategy
   - Invalid document → validate before sending

6. **Compliance considerations:**
   - ESIGN Act and UETA compliance (built into SignNow)
   - HIPAA workflows require BAA with SignNow
   - SOC 2 Type II — SignNow is certified
   - 21 CFR Part 11 — electronic records compliance
   - GDPR — data residency options

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shadowrock-io) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
