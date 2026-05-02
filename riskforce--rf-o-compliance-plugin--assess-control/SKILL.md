---
name: assess-control
description: Analyze codebase for NIST 800-53 Rev5 compliance control implementation and generate structured evidence findings at the assessment objective level. Use when assessing security controls, generating compliance evidence, or performing automated control assessments. Use when this capability is needed.
metadata:
  author: riskforce
---

# RFO Compliance Control Assessment

You are performing an automated compliance assessment of a codebase against NIST 800-53 Rev5 security controls. Your job is to examine the actual code and produce structured, evidence-backed findings at the **assessment objective level** -- the lowest automatable unit of compliance.

## CRITICAL: Step 0 -- Select system and choose assessment mode

Before analyzing any code:

1. **Select a system.** Call `select_system` (with no arguments) to list all systems the user has access to. Present the list and ask the user which system to assess against. Then call `select_system` again with the chosen `system_id` to activate it.
2. **Ask the user which description mode to use:**
   - **Redacted Mode** (recommended for most users) -- Descriptions use generic technical terms. No file names, function names, schema names, route paths, or proprietary implementation details are included. Safe to store in any third-party database.
   - **Detailed Mode** -- Descriptions include specific file names and technical details for maximum auditability. Use only when the organization is comfortable with implementation details being stored externally.
3. **Ask about database review:**
   - "Do you have a database you want reviewed as part of this assessment?"
     - **Yes, provide connection details** -- Ask the user for the connection method and credentials (host, port, database, user, password, SSL mode). Claude will query the schema directly for more accurate evidence gathering.
     - **Yes, provide a schema file** -- The user can provide a schema dump or DDL file. Note: this may produce a less accurate evaluation than a live connection since Claude cannot verify runtime behavior, constraints, or triggers.
     - **No database** -- Proceed with code-level assessment only.
4. **Ask about additional folders:**
   - "Do you have additional folders outside this git repo you'd like to include in the review? For example, a separate backend, frontend, or infrastructure repo."
   - If yes, have the user provide the path(s). Include those directories in Glob/Grep/Read searches during assessment.
5. **Ask about append mode:**
   - "Is this the only asset being assessed against this system, or do you plan to run the plugin against multiple assets (e.g., separate frontend, backend, database repos)?"
     - **Single asset** -- Normal mode (overwrites previous plugin findings).
     - **Multiple assets** -- Use append mode (`append: true`). Each assessment run adds to the existing implementation descriptions and evidence rather than replacing them. This builds up a comprehensive picture across assets but produces longer, compound descriptions.
6. **Ask which controls to assess:**
   - A specific control (e.g. "AC-02")
   - A specific family (e.g. "all AC controls")
   - All code-assessable controls

## Writing Conventions

### Implementation Description (control level)

Follow this convention for `control_implementation_description`:

```
Control objective is implemented in {layer} via {method}.
```

Where:
- `{layer}` = "the application backend", "the application frontend", "the database layer", "backend middleware", "the API layer", etc.
- `{method}` = a short, descriptive phrase about the technical approach

**Redacted Mode examples:**
- "Control objective is implemented in the application backend via role-based access control middleware with nine distinct permission levels."
- "Control objective is implemented in the database layer via structured audit logging with correlation tracking and automated event capture."
- "Control objective is implemented across backend and database layers via TLS enforcement, cryptographic hashing for credentials, and encrypted session management."

**Detailed Mode examples:**
- "Control objective is implemented in src/middleware/authorize.js via enforcePermissions() with role-based access checks against a policy engine."
- "Control objective is implemented in lib/audit/writer.js and the event_log table via structured JSON logging with trace IDs and caller context propagation."

### Implementation Description (objective level)

**Redacted Mode:** Describe the technical approach without naming files, functions, tables, or routes.
- "Event types are classified and recorded in structured log entries via application middleware that automatically maps request methods to audit categories."
- "User identity is resolved from authentication tokens and propagated through the request lifecycle to all downstream audit records."

**Detailed Mode:** Include specific references.
- "Event types are classified in lib/context.js:31 which maps HTTP methods to audit categories, and recorded in the event_log.category column."

### Assessment Notes

**Redacted Mode:**
- "Searched backend for audit evidence; found comprehensive coverage of event logging across authentication, authorization, data modification, and administrative actions. The database supports structured audit trails with before/after snapshots for data changes."

**Detailed Mode:**
- "Examined lib/audit/writer.js, src/middleware/context.js, and the event_log table schema. Found structured JSON logging with trace IDs, request metadata capture, and database-level audit trails via INSERT triggers."

### Evidence Array

The `evidence` array in the JSON file is for YOUR reference during analysis and is stored in metadata only. It does NOT flow into the system's official evidence_references.

**Redacted Mode:** Use generic descriptions.
```json
{"file": "backend/logging", "description": "Structured audit logging with event type classification"}
```

**Detailed Mode:** Use precise references.
```json
{"file": "lib/audit/writer.js", "line": 52, "description": "buildEntry() includes action/event type in structured JSON output"}
```

## Process

### Step 1: Get the control list

- Call `get_assessable_controls` to get the lightweight list of assigned controls
- Each control includes: control_id, title, prose (with ODPs resolved), current_status, and objective_count
- If the user specifies a family (e.g. "AC"), pass the family filter
- If the user specifies a specific control, still get the list to verify it exists, then proceed to that control

### Step 2: For each control, get objectives then analyze

For each control you need to assess:

1. **Call `get_control_objectives`** with the control_id to get the hierarchical objective tree
2. **CRITICAL: Use ONLY the exact `objective_label` values returned by `get_control_objectives`.** Each control has specific labels in the database (e.g. `au-2_obj.a`, `au-2_obj.c-1`, `ac-2_obj.l-1`). Do NOT invent, abbreviate, or guess labels. If you submit a label that doesn't exist in the database, it is **silently skipped** and your finding is lost. If you did not call `get_control_objectives` for a control, you do not know its labels.
3. **Filter out non-technical objectives.** Many assessment objectives are procedural, administrative, or documentation-related and CANNOT be assessed through code analysis. **Omit these entirely from your findings** -- do not include them with a "skipped" or "not_applicable" status. Simply leave them out.

   **Skip objectives whose prose asks to verify that:**
   - A policy, procedure, or plan is developed, documented, disseminated, or reviewed
   - Personnel, roles, or managers are designated, assigned, or notified
   - Approvals are required or obtained
   - A process is established, defined, or documented (not implemented in code)
   - Training, awareness, or communication activities occur
   - Reviews, assessments, or audits are conducted on a schedule
   - Compliance with organizational policies or external regulations is maintained
   - Terms and conditions, agreements, or MOUs are established

   **Keep objectives whose prose asks to verify that:**
   - The system enforces, implements, or restricts something technically
   - Access is authorized, controlled, monitored, or revoked via system mechanisms
   - Events are logged, captured, or recorded by the system
   - Data is encrypted, hashed, protected, or transmitted securely
   - Inputs are validated, sanitized, or checked
   - Sessions, accounts, or connections are managed by system logic
   - Configuration settings are applied or enforced

   When in doubt, ask: "Could a developer verify this by reading code?" If yes, keep it. If it requires reviewing a Word document or checking an org chart, skip it.

4. **Analyze the codebase** against each remaining technical objective:
   - Use Glob and Grep to find relevant code patterns
   - Use Read to examine specific files and confirm findings
   - Note your findings internally with precise references
5. **Assign per-objective status** based on evidence found
6. **Write descriptions** according to the chosen mode (Redacted or Detailed)

### Step 3: Determine per-objective status

For each assessment objective, assign a status:
- **implemented** - Strong evidence the objective is fully addressed in code
- **partially_implemented** - Some aspects are addressed but gaps exist
- **planned** - Code structure suggests intent but implementation is incomplete
- **not_implemented** - No evidence found in the codebase
- **not_applicable** - The objective does not apply to this type of application

Then roll up to an overall control status:
- If ALL objectives are implemented -> control is **implemented**
- If SOME are implemented -> control is **partially_implemented**
- If NONE are implemented -> control is **not_implemented**
- Use your judgment for mixed cases

### Step 4: Set confidence level
- **high** - Multiple clear code-level evidence points directly address the objective
- **medium** - Evidence exists but may be indirect or partially observable from code alone
- **low** - Limited evidence; control may be addressed at infrastructure/operational level beyond code

### Step 5: Write findings and SAVE TO DISK

Generate structured findings and **immediately write them to `.rf-o-findings.json` in the current working directory** before attempting to push. This file is automatically deleted after a successful upload.

The file format:
```json
{
  "assessed_at": "ISO-8601 timestamp",
  "assessed_by": "claude-code",
  "framework": "rev5",
  "source": "Claude Code",
  "mode": "redacted",
  "findings": [
    {
      "control_id": "AC-02",
      "control_status": "partially_implemented",
      "control_implementation_description": "Control objective is implemented in ...",
      "confidence": "high",
      "objectives": [
        {
          "objective_label": "ac-2_obj.a",
          "status": "implemented",
          "implementation_description": "...",
          "assessment_notes": "...",
          "evidence": [...]
        }
      ]
    }
  ]
}
```

### Step 6: Privacy review BEFORE pushing

After writing findings to disk, review them for privacy compliance:

1. **Scan all `implementation_description` and `assessment_notes` fields** across every control and objective
2. **In Redacted Mode**, flag any response that contains:
   - Specific file names or paths (e.g., "src/middleware/authorize.js")
   - Function or method names (e.g., "enforcePermissions()")
   - Database table or column names (e.g., "event_log.category")
   - Route paths (e.g., "/api/users/:id")
   - Environment variable names
   - Third-party library names that reveal architecture
3. **Report your review to the user:**

If all findings are clean:
> "I've reviewed the assessment and all results use appropriately generic technical descriptions. Ready to push."

If some findings need attention:
> "I've reviewed the assessment and recommend you review these N responses to ensure they meet your privacy guidelines:
> - AU-03 objective au-3_obj.d: mentions specific middleware name
> - SC-08 control description: references specific configuration file
>
> I can revise them per your instruction or keep them as-is."

4. **In Detailed Mode**, still inform the user:
> "Assessment is in detailed mode -- implementation details include specific file names and function references. Ready to push."

5. **Wait for user confirmation** before proceeding to push.

### Step 7: Submit findings

Use the **direct file upload** method to avoid sending large JSON through the context window:

1. **Call `prepare_findings_upload`** with:
   - `system_id` -- the system UUID from Step 0 (you remembered it when the user selected it)
   - `append` -- true if the user chose append mode in Step 0
   - `source` -- defaults to "Claude Code"
2. **Run the returned curl command** via Bash. This uploads `.rf-o-findings.json` directly from disk without passing through the context window.
3. **Report results** to the user (succeeded/failed counts).

**Fallback:** If curl fails (e.g., network issue, sandbox restriction), read `.rf-o-findings.json` and pass the content to `push_findings_from_content` instead.

- Do NOT use `push_objective_findings` or `push_finding` manually unless all other methods fail and you need to retry individual controls.
- If all push methods fail, tell the user their findings are saved in `.rf-o-findings.json`.

### Step 8: Post-push cleanup

After a successful push:
1. **Delete `.rf-o-findings.json`** automatically -- it has already been pushed to RFO.
2. Inform the user: "Findings pushed to RFO successfully. The local findings file has been cleaned up."
3. If the user wants to keep a copy, offer to save it to a location of their choice before deleting.

### Step 9: On push failure

If the API call fails:
1. Tell the user their findings are saved in `.rf-o-findings.json`
2. Explain the error (authentication issue, network issue, etc.)
3. Help them fix the issue
4. Re-read `.rf-o-findings.json` and retry the push -- do NOT re-analyze the codebase

## Important Guidelines

- Only assess what is observable in the code. Do not assume infrastructure-level controls.
- If an objective is primarily operational/procedural (not code-level), mark confidence as "low" and note that in the objective's assessment_notes.
- Do not fabricate evidence. If you cannot find it, say so.
- Normalize control IDs to uppercase with hyphens (e.g. "SC-02" not "sc02" or "SC 02").
- ALWAYS save findings to `.rf-o-findings.json` before pushing. The analysis is expensive; the push is cheap and retriable.
- ALWAYS do the privacy review before pushing. The user trusts you to protect their IP.
- The legacy `push_finding` and `push_batch_findings` tools still work at the control level but `push_findings_from_content` is strongly preferred.

## Example: Redacted Mode Finding

```json
{
  "control_id": "AC-03",
  "control_status": "implemented",
  "control_implementation_description": "Control objective is implemented in the application backend via multi-tier role-based access control middleware. All API routes are protected by token-based authentication followed by role and permission verification at the system, organization, and platform levels.",
  "confidence": "high",
  "objectives": [
    {
      "objective_label": "ac-3_obj.1",
      "status": "implemented",
      "implementation_description": "Approved authorizations for logical access are enforced through backend middleware that validates personnel role assignments against a permission model before allowing route handler execution. Nine distinct roles provide granular access control.",
      "assessment_notes": "Searched backend middleware for access enforcement; found comprehensive role-based permission checking applied to all protected API routes. Unauthorized access attempts return structured error responses.",
      "evidence": [
        {"file": "backend/access-control", "description": "Role-based permission validation middleware"},
        {"file": "backend/route-protection", "description": "Authentication enforcement wrapper on all tenant routes"}
      ]
    }
  ]
}
```

## Example: Detailed Mode Finding

```json
{
  "control_id": "AC-03",
  "control_status": "implemented",
  "control_implementation_description": "Control objective is implemented in src/middleware/authorize.js via enforcePermissions() with policy-based role validation. All protected routes in src/routes/index.js are wrapped with requireAuth middleware.",
  "confidence": "high",
  "objectives": [
    {
      "objective_label": "ac-3_obj.1",
      "status": "implemented",
      "implementation_description": "Approved authorizations enforced through enforcePermissions() which validates user role assignments against a policy engine. Five roles: Admin, Manager, Operator, Viewer, Auditor.",
      "assessment_notes": "Examined src/middleware/authorize.js:34 verifyRole() and src/routes/index.js:112 requireAuth wrapper. All protected routes return 403 with structured error on unauthorized access.",
      "evidence": [
        {"file": "src/middleware/authorize.js", "line": 34, "description": "verifyRole() validates active role assignments against policy rules"},
        {"file": "src/routes/index.js", "line": 112, "description": "All protected routes wrapped with requireAuth middleware enforcing authentication"}
      ]
    }
  ]
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/riskforce) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
