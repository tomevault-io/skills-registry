---
name: ado-bugs
description: Update Azure DevOps bugs with investigation findings. Use when asked to update bug, add findings to bug, document bug investigation, or work with ADO bug items. Routes content to System Info field (if empty) or Comments (if System Info has data). Use when this capability is needed.
metadata:
  author: klemensms
---

version: "1.0"
author: Klemens Stelk

# ADO Bugs

## Workflow

1. **Fetch**: When given an ADO bug number (e.g., 1234), fetch via MCP server using work item ID
2. **Check System Info**: Determine if the "System Info" field is empty or contains data
3. **Route Content**:
   - **Empty System Info** → Add findings to the System Info field
   - **System Info has data** → Add findings as a comment
4. **Update**: Push changes via MCP server (default) unless user requests output only

## Decision Logic

```
IF System Info field is empty or null:
    → Format findings as HTML
    → Update the System Info field
ELSE:
    → Format findings as HTML comment
    → Add as a new comment to the work item
```

## ADO Links

- Work item URL: `https://dev.azure.com/NationalEducationUnion/Membership%20System%20Replacement/_workitems/edit/{ADO-number}`
- **All links must open in new tab** using `target="_blank"`:
  ```html
  <a href="https://example.com/page" target="_blank">Link Text</a>
  ```
- Internal ADO link format (for referencing other work items):
  ```html
  <a href="https://dev.azure.com/NationalEducationUnion/Membership%20System%20Replacement/_workitems/edit/{number}/" target="_blank" data-vss-mention="version:1.0">#{number}</a>
  ```

## System Info Field Template (HTML)

Use this structure when adding findings to an empty System Info field:

```html
<div>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><b>Investigation Findings</b></p>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;">[Summary of investigation]</p>

<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><br></p>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><b>Root Cause</b></p>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;">[Root cause analysis]</p>

<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><br></p>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><b>Technical Details</b></p>
<ul style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;">
<li>[Detail 1]</li>
<li>[Detail 2]</li>
</ul>

<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><br></p>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><b>Recommended Fix</b></p>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;">[Proposed solution]</p>

<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><br></p>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><b>Related Items</b></p>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;">See <a href="https://dev.azure.com/NationalEducationUnion/Membership%20System%20Replacement/_workitems/edit/{number}/" target="_blank" data-vss-mention="version:1.0">#{number}</a></p>
</div>
```

## Comment Template (HTML)

Use this structure when System Info already has data:

```html
<div>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><b>Additional Findings</b></p>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;">[Investigation findings here]</p>

<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><br></p>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><b>Details</b></p>
<ul style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;">
<li>[Finding 1]</li>
<li>[Finding 2]</li>
</ul>

<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><br></p>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;">Reference: <a href="https://docs.microsoft.com/example" target="_blank">Documentation Link</a></p>
</div>
```

## HTML Formatting Rules

### Required for All Content

- Wrap all content in `<div>` tags
- Use `<p>` tags with Segoe UI font styling for paragraphs
- Use `<b>` for bold headers/emphasis
- Use `<br>` for line breaks within content

### Links (Critical)

**Every link MUST include `target="_blank"`** to open in a new tab:

```html
<!-- External links -->
<a href="https://example.com" target="_blank">Link Text</a>

<!-- ADO work item links -->
<a href="https://dev.azure.com/NationalEducationUnion/Membership%20System%20Replacement/_workitems/edit/1234/" target="_blank" data-vss-mention="version:1.0">#1234</a>

<!-- Documentation links -->
<a href="https://learn.microsoft.com/..." target="_blank">Microsoft Docs</a>
```

### Lists

```html
<ul style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;">
<li>[Item 1]</li>
<li>[Item 2]</li>
</ul>
```

### Code Snippets

```html
<code style="font-family:Consolas,monospace;background-color:#f4f4f4;padding:2px 4px;">[code here]</code>
```

## Verification Testing

When investigation reveals the reported functionality **actually works** and the issue lies elsewhere (e.g., orphaned data, unrelated errors in logs):

### Required Actions

1. **Prove it works**: Actively test the reported scenario
   - Make the API call/PATCH/POST that was reported as failing
   - Wait for async operations (e.g., 90 seconds for SCC sync)
   - Verify the expected outcome in the target system

2. **Document the proof**: Add verification evidence to the bug
   - Timestamp of test
   - Request/response details
   - Before/after values showing successful operation

### Verification Template (HTML)

```html
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><b>Verification Test</b></p>
<table border="1" cellpadding="5" style="font:14px &quot;Segoe UI&quot;;">
<tr><th>Test</th><th>Result</th></tr>
<tr><td>PATCH <code>msdynmkt_value</code> to 534120001</td><td>✓ Success (HTTP 200)</td></tr>
<tr><td>Wait 90s for sync</td><td>✓ Complete</td></tr>
<tr><td>Verify in Dataverse</td><td>✓ Value updated to 534120001</td></tr>
</table>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><br></p>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><b>Conclusion:</b> The reported operation works correctly. The errors shown were from unrelated orphaned records.</p>
```

### Common Verification Scenarios

| Scenario | How to Verify |
|----------|---------------|
| DAB REST API PATCH | Make PATCH call via REST MCP, wait for sync, check Dataverse |
| Stored Procedure | Execute via SQL MCP, verify results in database |
| Dataverse Plugin | Check plugin trace logs for success entries |
| Flow/Workflow | Check flow run history for successful executions |

## Section Guidelines

Adapt sections based on investigation type. Not all sections are required:

| Section | When to Include |
|---------|-----------------|
| Investigation Findings | Always - main summary |
| Root Cause | When cause is identified |
| Technical Details | For complex bugs |
| Verification Test | When reported issue actually works |
| Recommended Fix | When solution is known |
| Steps to Reproduce | If clarified during investigation |
| Related Items | When other work items are relevant |
| Environment Details | For environment-specific bugs |

## CRM Bug Fixes - Off-Limits (Manual Tasks)

When a bug fix involves CRM/Dataverse changes, these are better done manually by the user. **Do not attempt these - document them as manual tasks instead:**

| Off-Limits | Reason |
|------------|--------|
| Form customization | Unless building a new table/form from scratch, add as manual task for user |
| Model-driven app creation | Better done manually in Power Apps |
| Model-driven app customization | Better done manually in Power Apps |

When these are needed as part of a fix, add to the comment:

```html
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><b>Manual Tasks Required:</b></p>
<ul style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;">
<li>Update form to show/hide field: [field name]</li>
<li>Update model-driven app navigation (if needed)</li>
</ul>
```

## Bug State Transitions

| When | Action |
|------|--------|
| Fix implemented | Add findings comment, move to "Testing" |
| After testing passes | Move to "Closed" |

**Note:** Do NOT move directly to Resolved/Closed. Always move to "Testing" first.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/klemensms) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
