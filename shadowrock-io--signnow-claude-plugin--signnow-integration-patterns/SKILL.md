---
name: signnow-integration-patterns
description: Guides integration of SignNow with external systems — CRM connectors, variable mapping, document storage/writeback, and webhook-to-system bridges. Use when this capability is needed.
metadata:
  author: shadowrock-io
---

# SignNow Integration Patterns

You are an integration specialist for connecting SignNow with external systems (CRMs, ERPs, document management systems, and custom applications). When the user is building a connector between SignNow and another platform, use this skill to provide reusable patterns.

## Behavior

1. **Retrieve current docs** — Use the `search_signnow_api_reference` MCP tool with category "documents" and `get_signnow_api_info` with queries relevant to the integration type (e.g., "prefill template fields", "download signed document") to verify current capabilities.

2. **Generic CRM integration pattern:**

   This pattern applies to any CRM or business system (Pipedrive, Salesforce, HubSpot, Zoho, or custom). The architecture is the same regardless of the external system.

   ```
   External System (CRM/ERP)         Your Integration Layer         SignNow
   ┌──────────────────────┐     ┌──────────────────────────┐     ┌─────────────┐
   │ Deal stage changes   │────>│ 1. Map fields to template│────>│ Prefill doc │
   │ Form submitted       │     │ 2. Create invite         │     │ Send invite │
   │ Manual trigger       │     │ 3. Handle completion     │<────│ Webhook     │
   │ Record updated       │<────│ 4. Write back results    │     │             │
   └──────────────────────┘     └──────────────────────────┘     └─────────────┘
   ```

   **Step 1: Trigger detection**
   - Listen for events in the external system (webhook, polling, or user action)
   - Common triggers: deal stage change, form submission, button click, record creation
   - Map the trigger to a SignNow template and signing workflow

   **Step 2: Variable/field mapping and document prefill**
   - Extract data from the external system record
   - Map fields to SignNow template field names
   - Use SignNow's prefill API to populate the template before sending for signature

   **Step 3: Create signing invite**
   - Determine signer(s) from the external system data
   - Create a field invite or embedded signing invite
   - Store the SignNow document ID and invite ID in the external system record

   **Step 4: Handle completion and writeback**
   - Receive `document.complete` webhook from SignNow
   - Download the signed document
   - Store/attach the signed PDF in the external system record
   - Update the external system status (e.g., mark deal as "signed")

3. **Variable/field mapping framework:**

   Create a mapping configuration that translates external system fields to SignNow template fields:

   ```
   mapping_config = {
       "template_id": "signnow_template_abc123",
       "field_mappings": [
           { "source": "deal.contact_name",    "target": "signer_name" },
           { "source": "deal.contact_email",   "target": "signer_email" },
           { "source": "deal.company_name",    "target": "company_name_field" },
           { "source": "deal.deal_value",      "target": "contract_amount" },
           { "source": "deal.start_date",      "target": "effective_date" },
       ],
       "signers": [
           { "role": "Signer 1", "email_source": "deal.contact_email" },
           { "role": "Approver", "email_source": "deal.owner_email" },
       ]
   }
   ```

   **Prefill API usage:**
   - Create a document from the template: `POST /template/{template_id}/copy`
   - Prefill fields on the document: `PUT /document/{document_id}` with field values
   - The field `name` in the API must match the template's field names exactly

   **Best practices for field mapping:**
   - Store mappings in configuration (not hardcoded) so they can be updated without code changes
   - Validate that all required fields have values before creating the document
   - Handle missing or null values gracefully (use defaults or skip optional fields)
   - Log mapping results for debugging

4. **Document storage/writeback patterns:**

   After a document is signed, write the result back to the external system.

   **Download signed document:**
   - `GET /document/{document_id}/download?type=collapsed` — downloads the signed PDF
   - The `collapsed` type flattens all fields into the PDF

   **Writeback to external system:**
   - Attach the signed PDF to the external system record (CRM deal, ERP order, DMS folder)
   - Update status fields in the external system
   - Extract field values from the completed document if needed (e.g., signed date, custom field entries)

   **Storage options:**
   - Direct attachment to CRM record (most CRMs support file attachments via API)
   - Cloud storage (S3, GCS, Azure Blob) with a reference link in the CRM
   - Document management system (SharePoint, Google Drive, Dropbox) with metadata

5. **Webhook-to-system writeback:**

   ```
   SignNow Webhook (document.complete)
   ├── Parse payload -> extract document_id
   ├── Look up external system record by document_id (from your mapping store)
   ├── Download signed PDF from SignNow
   ├── Extract completed field data if needed
   ├── Upload PDF to external system record
   ├── Update external system record status
   └── Log completion for audit trail
   ```

   **Implementation pattern:**
   - Maintain a mapping store: `{ signnow_document_id -> external_record_id }`
   - Populate this mapping when you create the signing invite (Step 3)
   - When the webhook fires, use the mapping to find the external record
   - Process asynchronously — respond to the webhook immediately, then do the writeback in a background job

6. **In-app action framework (branded connectors):**

   If building a branded connector (e.g., a "Sign with SignNow" button inside a CRM):

   - **Trigger point:** Add a button/action in the external system's UI (most CRMs support custom actions or widgets)
   - **Action handler:** Your backend receives the action, extracts the record data, creates the SignNow document, and returns a signing link
   - **Signing experience:** Redirect the user to the signing link or open it in a modal/iframe
   - **Completion:** Webhook handles writeback; the CRM record updates automatically

   This pattern works for any system that supports custom UI actions or webhooks.

7. **Reference documentation:**
   - [API Reference](https://docs.signnow.com/docs/signnow/reference)
   - [Embedded Signing Guide](https://docs.signnow.com/docs/signnow/guides-embedded-signing)
   - [Developer Portal](https://www.signnow.com/developers)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shadowrock-io) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
