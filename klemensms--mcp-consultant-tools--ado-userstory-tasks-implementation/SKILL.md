---
name: implement-ado-user-story-or-task
description: Implement ADO user stories or tasks with structured planning and documentation. Use when asked to implement user story, implement task, work on story, work on task, build feature, complete work item, or update implementation progress. Presents implementation plan first, then documents work in comments with HTML formatting. Use when this capability is needed.
metadata:
  author: klemensms
---

version: "1.0"
author: Klemens Stelk

# ADO User Story & Task Implementation

This skill applies to implementing **both User Stories and Tasks** - the workflow is identical.

## Workflow

1. **Fetch**: When given an ADO work item number (e.g., 1234), fetch via MCP server using work item ID
2. **Understand**: Read description, acceptance criteria, and any parent/child context
3. **Plan**: Create implementation plan and present to user for approval BEFORE starting work
4. **Implement**: Execute the approved plan
5. **Document**: Add progress/completion updates as HTML comments
6. **Release Notes**: Update release notes with changes made

## Implementation Planning (Required)

**ALWAYS present an implementation plan before starting work.**

### Planning Steps

1. Analyze the requirements from Description field
2. Review Acceptance Criteria
3. Check parent/child work items for context
4. Identify affected components/files
5. Break down into numbered implementation steps
6. Present plan to user and wait for approval

### Plan Template (present to user)

```markdown
## Implementation Plan for #{number}

**Work Item**: [Title]
**Type**: [User Story / Task]
**Parent**: #{parent-number} - [Parent title] (if applicable)

### Scope
- [What will be changed/built]

### Implementation Steps
1. [Step 1 - specific action]
2. [Step 2 - specific action]
3. [Step 3 - specific action]
...

### Files Affected
- `path/to/file1.ts` - [what changes]
- `path/to/file2.cs` - [what changes]

### Testing Approach
- [How to verify the implementation]

### Risks/Considerations
- [Any risks or things to watch for]

**Ready to proceed?**
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

## Comment Templates (HTML)

### Implementation Started Comment

Add when beginning work:

```html
<div>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><b>Implementation Started</b></p>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><br></p>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><b>Plan:</b></p>
<ol style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;">
<li>[Step 1]</li>
<li>[Step 2]</li>
<li>[Step 3]</li>
</ol>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><br></p>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><b>Files to modify:</b></p>
<ul style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;">
<li><code style="font-family:Consolas,monospace;background-color:#f4f4f4;padding:2px 4px;">[file path]</code></li>
</ul>
</div>
```

### Progress Update Comment

Add when significant progress is made or when pausing work:

```html
<div>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><b>Progress Update</b></p>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><br></p>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><b>Completed:</b></p>
<ul style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;">
<li>[Completed item 1]</li>
<li>[Completed item 2]</li>
</ul>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><br></p>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><b>Remaining:</b></p>
<ul style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;">
<li>[Remaining item 1]</li>
<li>[Remaining item 2]</li>
</ul>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><br></p>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><b>Notes:</b> [Any blockers, decisions, or observations]</p>
</div>
```

### Implementation Complete Comment

Add when work is fully implemented:

```html
<div>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><b>Implementation Complete</b></p>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><br></p>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><b>Summary:</b></p>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;">[Brief description of what was implemented]</p>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><br></p>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><b>Changes Made:</b></p>
<table border="1" cellpadding="5" style="font:14px &quot;Segoe UI&quot;;">
<tr><th>File</th><th>Change</th></tr>
<tr><td><code style="font-family:Consolas,monospace;">[file path]</code></td><td>[Description of change]</td></tr>
<tr><td><code style="font-family:Consolas,monospace;">[file path]</code></td><td>[Description of change]</td></tr>
</table>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><br></p>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><b>Testing:</b></p>
<ul style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;">
<li>[Test performed 1] - &#10003; Passed</li>
<li>[Test performed 2] - &#10003; Passed</li>
</ul>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><br></p>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><b>Commit:</b> <code style="font-family:Consolas,monospace;background-color:#f4f4f4;padding:2px 4px;">[commit hash]</code></p>
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><b>PR:</b> <a href="[PR URL]" target="_blank">[PR title/number]</a></p>
</div>
```

## Release Notes Update

After completing implementation, update the project release notes.

### Release Notes Entry Format

Add entry under appropriate section (Features, Fixes, Changes):

```markdown
- **#{work-item-number}**: [Brief description of what was implemented]
```

### Release Notes Location

Check project for:
- `CHANGELOG.md`
- `docs/release-notes/`
- `RELEASE_NOTES.md`

If no release notes file exists, create entry in the completion comment instead.

## HTML Formatting Rules

### Required for All Content

- Wrap all content in `<div>` tags
- Use `<p>` tags with Segoe UI font styling for paragraphs
- Use `<b>` for bold headers/emphasis
- Use `<br>` for line breaks within content

### Lists

```html
<!-- Unordered -->
<ul style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;">
<li>[Item 1]</li>
<li>[Item 2]</li>
</ul>

<!-- Ordered -->
<ol style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;">
<li>[Step 1]</li>
<li>[Step 2]</li>
</ol>
```

### Tables

```html
<table border="1" cellpadding="5" style="font:14px &quot;Segoe UI&quot;;">
<tr><th>Header 1</th><th>Header 2</th></tr>
<tr><td>Cell 1</td><td>Cell 2</td></tr>
</table>
```

### Code References

```html
<code style="font-family:Consolas,monospace;background-color:#f4f4f4;padding:2px 4px;">[code/path here]</code>
```

### Links (Critical)

**Every link MUST include `target="_blank"`** to open in a new tab:

```html
<!-- External links -->
<a href="https://example.com" target="_blank">Link Text</a>

<!-- ADO work item links -->
<a href="https://dev.azure.com/NationalEducationUnion/Membership%20System%20Replacement/_workitems/edit/1234/" target="_blank" data-vss-mention="version:1.0">#1234</a>

<!-- PR/Git links -->
<a href="https://dev.azure.com/org/project/_git/repo/pullrequest/123" target="_blank">PR #123</a>
```

## Off-Limits (Manual Tasks)

These are better done manually by the user. **Do not attempt these - add them as manual tasks instead:**

| Off-Limits | Reason |
|------------|--------|
| Form customization | Unless building a new table/form from scratch, add as manual task for user |
| Model-driven app creation | Better done manually in Power Apps |
| Model-driven app customization | Better done manually in Power Apps |

When these are needed, add to the Implementation Complete comment:

```html
<p style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;"><b>Manual Tasks Required:</b></p>
<ul style="box-sizing:border-box;font:14px &quot;Segoe UI&quot;;margin:0px;">
<li>Add new field(s) to form: [field names]</li>
<li>Update model-driven app navigation (if needed)</li>
</ul>
```

## Work Item State Transitions

| When | Action |
|------|--------|
| Starting work | Move to "Active", add "Implementation Started" comment |
| Making progress | Add "Progress Update" comment |
| Work complete | Add "Implementation Complete" comment, move to "Testing" |
| After testing passes | Move to "Closed" |

**Note:** Do NOT move directly to Resolved/Closed. Always move to "Testing" first.

## Checklist Before Moving to Testing

- [ ] All planned changes implemented
- [ ] Code committed and pushed
- [ ] PR created (if applicable)
- [ ] Tests passing
- [ ] Implementation Complete comment added to ADO
- [ ] Manual tasks documented (if any CRM form/app changes needed)
- [ ] Release notes updated
- [ ] Work item state moved to "Testing"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/klemensms) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
