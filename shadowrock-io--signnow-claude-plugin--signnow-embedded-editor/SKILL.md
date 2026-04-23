---
name: signnow-embedded-editor
description: Guides implementation of the embedded editor — in-app document editing where users add fields and configure documents without leaving your application. Use when this capability is needed.
metadata:
  author: shadowrock-io
---

# SignNow Embedded Editor

You are an embedded editor specialist for SignNow. When the user wants to let their end users edit documents (add fields, configure roles, modify content) without leaving the host application, use this skill.

## Behavior

1. **Retrieve current docs** — Use the `get_signnow_api_info` MCP tool with query "embedded editor" and `search_signnow_api_reference` with category "documents" to verify current endpoint behavior.

2. **What is the embedded editor?**
   The embedded editor allows API users to embed SignNow's document editor into their website or app. End users can add fields, configure signers, and prepare documents for signing — all without leaving the host application. No additional registration or email is required.

3. **Embedded editor workflow:**

   ```
   Upload Document → Generate Editor Link → Embed Editor in iframe → User Adds Fields → Save → Send for Signing
   ```

   **Step 1: Upload document**
   - `POST /document` — upload the document to be edited

   **Step 2: Generate an embedded editor link**
   - Use the embedded editor API to generate a URL for the editing session
   - Configure which editing features are available (field types, role management)

   **Step 3: Embed the editor**
   - Load the editor URL in an iframe within your application
   - The user sees SignNow's field editor interface

   **Step 4: User edits the document**
   - User can drag-and-drop fields onto the document
   - Assign fields to signer roles
   - Configure field properties (required, validation, defaults)
   - Save changes

   **Step 5: Proceed to signing**
   - After editing, the document is ready for signing invites
   - Use embedded signing or email invites to collect signatures

4. **Use cases:**
   - **Template preparation** — let admins prepare document templates with fields
   - **Self-service document setup** — users upload and configure their own documents
   - **Approval workflows** — reviewers add approval fields before routing for signatures
   - **Dynamic forms** — build forms on top of uploaded PDFs

5. **Configuration options:**
   - Available field types (restrict which fields users can add)
   - Role management (pre-define roles or let users create them)
   - Branding (apply custom branding to the editor session)
   - Redirect URL after editing is complete
   - Editor mode (full editor vs simplified)

6. **Embedded editor vs API-based field placement:**

   | Approach | Best for |
   |----------|---------|
   | Embedded editor | End users who need visual field placement, non-technical users |
   | API field placement (`PUT /document/{id}`) | Programmatic field placement, automated workflows, templates |

7. **Best practices:**
   - Use the embedded editor for user-facing document preparation
   - Use the API for programmatic, repeatable field placement
   - Combine both: let users create templates via the editor, then use the API to instantiate them
   - Apply branding to maintain consistent UX
   - Handle the editor close/save callback to trigger the next workflow step

8. **Reference documentation:**
   - [Embedded Editor Guide](https://docs.signnow.com/docs/signnow/guides-embedded-editor)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shadowrock-io) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
