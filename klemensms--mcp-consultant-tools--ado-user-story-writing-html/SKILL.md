---
name: ado-user-story-writing
description: Create, update, and improve Azure DevOps user stories. Use when asked to create user story, write user story, update user story, improve user story, refine user story, or work with ADO items. Handles Description field (HTML) and Acceptance Criteria field separately. Use when this capability is needed.
metadata:
  author: klemensms
---

version: "1.0"
author: Klemens Stelk

# ADO User Stories

## Workflow

1. **Fetch**: When given an ADO number (e.g., 1234), fetch via MCP server using work item ID
2. **Edit**: Create/modify content following templates below
3. **Update**: Push changes via MCP server (default) unless user requests output only

## Format

Unless specifically instructed differently by the user (to use markdown formatting), all fields and comments need to be html formatted. 

## ADO Links

- Work item URL: `https://dev.azure.com/NationalEducationUnion/Membership%20System%20Replacement/_workitems/edit/{ADO-number}`
- Internal link format (for referencing other stories):
  ```html
  <a href="https://dev.azure.com/NationalEducationUnion/Membership%20System%20Replacement/_workitems/edit/{number}/" target="_blank" data-vss-mention="version:1.0">#{number}</a>
  ```

## Description Field Template (HTML)

Use this structure. Remove irrelevant sections during refinement but start complete:

```html
<div>
<p style="box-sizing:border-box;font:18px &quot;Segoe UI&quot;;margin:0px 0px 14.9px;"><b>Client Requirement</b></p>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;">[Requirement text here]</p>

<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><br></p>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><b>Process Diagrams</b></p>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;">Figjam link: [link]</p>

<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><br></p>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><b>Assumptions</b> (optional)</p>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;">[Assumptions list]</p>

<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><br></p>
<p style="box-sizing:border-box;font:18px &quot;Segoe UI&quot;;margin:0px 0px 14.9px;"><b>Proposed Technical Approach</b></p>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;">[Technical approach details]</p>

<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><br></p>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><b>Prerequisites</b> (optional)</p>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;">[e.g.: SPO set up, Customer Insights configured, outgoing mailboxes defined & available]</p>
</div>
```

## Acceptance Criteria Field  (HTML)

**Always use the dedicated AC field, not the Description field.**

### Format (strict)

```
**Business Rules**

| Rule | Description |
|---|---|
| [Rule name] | [Rule description] |

**Feature: [Feature Title]**

**AC1: [Scenario Name]**
**Given** [precondition]
**When** [action]
**Then** [expected outcome]
**And** [additional outcome if needed]

**AC2: [Scenario Name]**
**Given** [precondition]
**When** [action]
**Then** [expected outcome]
```

### Rules

- Number ACs sequentially (AC1, AC2, AC3...)
- Bold all keywords: **Given**, **When**, **Then**, **And**
- Bold AC headers: **AC1: Name**
- Each AC tests one scenario
- Label assumptions needing verification when scenarios aren't explicitly described

## Client Review Highlighting

Items requiring client sign-off/review: <span style="background-color:yellow;">[client review]</span>

## Comments (HTML Required)

**All comments added to ADO items must be HTML formatted.**

### Comment Template

```html
<div>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><b>[Comment Title/Subject]</b></p>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;">[Comment content here]</p>
</div>
```

### Comment Formatting Rules

- Wrap all content in `<div>` tags
- Use `<p>` tags with Segoe UI font styling for paragraphs
- Use `<b>` for bold emphasis
- Use `<br>` for line breaks within content
- For bullet lists:
  ```html
  <ul style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;">
  <li>[Item 1]</li>
  <li>[Item 2]</li>
  </ul>
  ```
- For linking to other work items in comments, use the internal link format (see ADO Links section above)

### Example Comment

```html
<div>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><b>Refinement Notes</b></p>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;">Discussed with client on 2024-01-15:</p>
<ul style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;">
<li>Confirmed email notification requirement</li>
<li>No SMS fallback needed</li>
<li>Related to <a href="https://dev.azure.com/NationalEducationUnion/Membership%20System%20Replacement/_workitems/edit/5678/" target="_blank" data-vss-mention="version:1.0">#5678</a></li>
</ul>
</div>
```

## Parent Linking

When parent item provided, set the parent work item link in ADO.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/klemensms) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
