---
name: substantive-testing
description: Run substantive attribute testing on audit samples. Use this when the user wants to create a test type, define attributes, upload supporting documents, execute tests against transaction samples, and export results to a local workbook. Use when this capability is needed.
metadata:
  author: tellenai
---

# Substantive Attribute Testing

Guide the user through Tellen's substantive attribute testing workflow. This skill orchestrates the full lifecycle: creating test types, defining attributes, uploading documents, executing tests, and exporting results.

## Overview

Substantive attribute testing validates individual transactions (samples) against defined audit attributes. Each attribute checks a specific assertion (e.g., "invoice date falls within the audit period", "vendor name matches the PO"). The Tellen AI agent reads uploaded supporting documents and evaluates each attribute, providing PASS/FAIL/EXCEPTION results with evidence citations.

## Workflow

### Step 1 — Choose or Create a Test Type

Ask the user what they're testing. Common test types include:
- **Disbursements / Accounts Payable** — Testing vendor payments against invoices, POs, and approvals
- **Revenue / Accounts Receivable** — Testing sales transactions against contracts and shipping docs
- **Payroll** — Testing payroll transactions against timesheets, rates, and approvals
- **Journal Entries** — Testing manual journal entries for authorization and support
- **Fixed Assets** — Testing asset additions/disposals against supporting documentation

Use the Tellen API to check for existing test types or create a new one.

<!-- TODO: Add MCP tool call for listing/creating test types once the API is exposed via MCP -->
<!-- The current MCP server exposes process_document, search_files, and research_us_gaap. -->
<!-- Test type CRUD and attribute testing execution need additional MCP tools. -->
<!-- For now, direct the user to the Tellen UI for test type creation. -->

**Current approach**: Direct the user to create the test type in the Tellen UI at **Substantive Testing → New Test Type**, then return here to continue with document upload and execution.

### Step 2 — Define Attributes

Each attribute is a testable assertion. Help the user think through what they need to verify. Example attributes for a disbursements test:

1. **Invoice Date** — Invoice date falls within the audit period
2. **Amount Agreement** — Invoice amount agrees to the payment amount
3. **Vendor Match** — Vendor on the invoice matches the vendor on the PO
4. **Authorization** — Payment was authorized per the approval matrix
5. **GL Coding** — Transaction was coded to the correct GL account

<!-- TODO: Add MCP tool call for creating attributes programmatically -->

**Current approach**: Attributes are defined in the Tellen UI within the test type. Guide the user through the attribute definition process there.

### Step 3 — Upload Supporting Documents

Use the `process_document` MCP tool to upload the user's supporting documents. These are the source materials the AI will reference when testing each sample.

For each document the user provides:

1. Read the file from the user's local filesystem
2. Base64-encode the file contents
3. Call `process_document` with the file name, base64 content, and MIME type
4. Note the returned `file_id` — this links the document to the workspace

Common document types:
- Invoices (PDF)
- Purchase orders (PDF, DOCX)
- Receiving reports (PDF, XLSX)
- Approval emails (PDF)
- Bank statements (PDF)
- Contracts (PDF, DOCX)

Wait for each document to finish processing before proceeding.

### Step 4 — Execute the Substantive Test

<!-- TODO: Add MCP tool for triggering substantive test execution -->
<!-- This requires exposing the SAMPLE_TEST background job creation via MCP -->

**Current approach**: Once documents are uploaded, direct the user to execute the test in the Tellen UI:
1. Go to the test type they created
2. Select the samples to test
3. Click **Run Test** to kick off the AI evaluation
4. Monitor progress in real-time as each attribute is tested

The AI agent will:
- Search uploaded documents for evidence relevant to each attribute
- Verify evidence against the attribute's assertion
- Return PASS, FAIL, or EXCEPTION with cited evidence and rationale

### Step 5 — Export Results

<!-- TODO: Add MCP tool for exporting test results -->
<!-- This requires an export/download endpoint exposed via MCP -->

**Current approach**: After tests complete, the user can export from the Tellen UI:
1. Go to the completed test
2. Click **Export Results**
3. Download the workbook (XLSX format)

Help the user understand the export format:
- One row per sample × attribute combination
- Columns: Sample ID, Attribute, Result (Pass/Fail/Exception), Evidence, Rationale
- Exception details include what evidence was missing

## Tips

- **Document quality matters** — Cleaner, higher-resolution documents yield better AI analysis
- **Be specific with attributes** — Vague attributes like "looks correct" produce unreliable results
- **Combine with search** — Use `search_files` to spot-check specific items across uploaded documents
- **Standards backup** — Use `research_us_gaap` if you need to verify the accounting standard behind an attribute

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tellenai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
