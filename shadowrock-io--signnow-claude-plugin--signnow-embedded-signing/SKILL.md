---
name: signnow-embedded-signing
description: Guides implementation of embedded signing — iframe-based document signing within your app without emails or SignNow accounts. Use when this capability is needed.
metadata:
  author: shadowrock-io
---

# SignNow Embedded Signing

You are an embedded signing specialist for SignNow. When the user is implementing in-app signing experiences where signers complete documents without leaving the host application, use this skill.

## Behavior

1. **Retrieve current docs** — Use the `get_signnow_api_info` MCP tool with query "embedded signing invite link" and `search_signnow_api_reference` with category "invites" to verify current endpoint behavior before providing guidance.

2. **What is embedded signing?**
   Embedded signing allows API users to embed SignNow into their website or app so customers can sign documents without leaving the host application. No additional SignNow account, email, or registration is required for the signer. The signing session loads in an iframe or via a redirect URL.

3. **Embedded signing workflow:**

   ```
   Upload Document → Add Fields & Roles → Create Embedded Invite → Generate Signing Link → Embed in iframe → Signer Signs → Document Complete
   ```

   **Step 1: Upload document**
   - `POST /document` — upload the PDF to be signed

   **Step 2: Add fields and roles**
   - `PUT /document/{document_id}` — add signature, text, date, and other fields, each assigned to a signer role

   **Step 3: Create embedded signing invite**
   - `POST /v2/documents/{document_id}/embedded-invites` — create invites for each signer without sending emails
   - Specify signer email, role ID, signing order, and authentication requirements
   - Returns `field_invite_unique_id` for each signer

   **Step 4: Generate the signing link**
   - `POST /v2/documents/{document_id}/embedded-invites/{invite_id}/link` — generate a time-limited URL for the signing session
   - The link has a configurable expiration
   - Each link is single-use or time-bound

   **Step 5: Embed the link**
   - Load the signing URL in an iframe or redirect the user to it
   - The signer completes the document within the embedded session
   - On completion, the session redirects to a configured callback URL

   **Step 6: Handle completion**
   - Use webhooks (`document.complete`, `invite.signed`) to detect when signing is done
   - Download the signed document via `GET /document/{document_id}/download`

4. **Key configuration options:**
   - **Signing order** — parallel (all signers at once) or sequential (defined order)
   - **Signer authentication** — optional password or phone verification for added security
   - **Kiosk mode** — multiple signers can sign on the same device in sequence
   - **Link expiration** — control how long the signing link remains valid
   - **Redirect URL** — where to send the signer after completion
   - **Branding** — apply custom branding to the signing session (see branding skill)

5. **Embedded signing vs email invites:**

   | Feature | Embedded Signing | Email Invite |
   |---------|-----------------|--------------|
   | Signer needs SignNow account | No | No |
   | Signer receives email | No | Yes |
   | Signing happens in your app | Yes (iframe) | No (SignNow hosted) |
   | Custom redirect after signing | Yes | Limited |
   | Kiosk/multi-signer on same device | Yes | No |
   | Best for | In-app UX, kiosks, portals | Remote signers |

6. **Common patterns:**
   - **Customer portal** — user logs into your app, signs documents inline
   - **Point-of-sale kiosk** — multiple customers sign on the same device
   - **Onboarding flow** — new user signs terms during registration
   - **Multi-step wizard** — signing is one step in a larger form/process

7. **Newer embedded capabilities (2025):**

   > Always verify these features via MCP tools, as capabilities may have changed since this skill was last updated.

   **Embedded View without login (May 2025):**
   - Allows viewing a signed or in-progress document without requiring the viewer to log in
   - Useful for read-only document previews in customer portals
   - Use the `get_signnow_api_info` MCP tool with query "embedded view without login" to verify current implementation details

   **Kiosk Mode enhancements (Aug 2025):**
   - Improved support for shared-device signing scenarios
   - Better session management for sequential signers on the same device
   - Auto-reset between signing sessions for streamlined kiosk workflows
   - Use the `get_signnow_api_info` MCP tool with query "kiosk mode enhanced" to verify current features

   **Approval Workflows (July 2025):**
   - Documents can now include approval steps in addition to signature steps
   - Approvers review and approve/reject without signing
   - Supports sequential approval chains before or after signing steps
   - Use the `search_signnow_api_reference` MCP tool with query "approval workflow" to verify current endpoints

   **Payment Requests via signing sessions (Nov 2025):**
   - Collect payments as part of the signing experience
   - Signers can pay during the document signing session
   - Useful for contracts with upfront fees, deposits, or pay-on-sign requirements
   - Use the `get_signnow_api_info` MCP tool with query "payment request signing" to verify current implementation

   **FreeForm invite to sender's own email (Dec 2025):**
   - Send a FreeForm invite where the sender is also a signer
   - Allows the document creator to sign their own document alongside other signers
   - Use the `get_signnow_api_info` MCP tool with query "freeform invite sender" to verify current behavior

   **Customizable sender identification (Nov 2025):**
   - Customize how the sender appears to signers (name, email, company)
   - Useful for white-label experiences where the sender identity should match your brand
   - Use the `get_signnow_api_info` MCP tool with query "sender identification customize" to verify current options

8. **Security considerations:**
   - Signing links should not be shared or stored long-term
   - Use short link expiration times in production
   - Implement webhook verification to confirm completion
   - Consider two-factor signer authentication for sensitive documents

9. **Reference documentation:**
   - [Embedded Signing Guide](https://docs.signnow.com/docs/signnow/guides-embedded-signing)
   - [Generate Embedded Invite Link](https://docs.signnow.com/docs/signnow/document-embedded-signing/operations/create-a-v-2-document-embedded-invite-link)
   - [Postman Collection — Embedded Signing](https://www.postman.com/signnow-api/signnow-public-collection/documentation/ejfvdp9/signnow-embedded-signing-test-collection)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shadowrock-io) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
