---
name: pr-review-fixer
description: > Use when this capability is needed.
metadata:
  author: przbadu
---

# PR Review Fixer

Review GitHub PR code review comments, analyze their validity, and apply fixes to the codebase.

## Workflow

1. Extract PR review comments
2. Analyze each comment for validity
3. Apply valid fixes
4. Summarize actions taken

## Step 1: Extract PR Review Comments

Try methods in order until one succeeds:

### Method A: Chrome Browser (preferred)

Use Chrome MCP tools to read the PR page directly:

```
1. Call mcp__claude-in-chrome__tabs_context_mcp to get current browser state
2. Call mcp__claude-in-chrome__tabs_create_mcp to open a new tab
3. Call mcp__claude-in-chrome__navigate to go to the PR URL
4. Navigate to the "Files changed" tab by appending /files to the PR URL
5. Call mcp__claude-in-chrome__get_page_text to extract all review comments
6. If comments are truncated or not fully visible, use mcp__claude-in-chrome__javascript_tool
   to extract structured review data from the DOM
```

JavaScript extraction pattern for GitHub PR review comments:

```javascript
// Extract review comments from GitHub PR Files Changed page
(() => {
  const comments = [];
  document.querySelectorAll('.review-comment, .comment-holder, [id^="discussion_r"]').forEach(el => {
    const file = el.closest('[data-path]')?.getAttribute('data-path')
      || el.closest('.file')?.querySelector('.file-header')?.getAttribute('data-path')
      || 'unknown';
    const body = el.querySelector('.comment-body, .review-comment-contents .markdown-body')?.innerText || '';
    const author = el.querySelector('.author, .timeline-comment-header .author')?.innerText || '';
    const line = el.closest('[data-line]')?.getAttribute('data-line') || '';
    if (body.trim()) {
      comments.push({ file, line, author, body: body.trim() });
    }
  });
  return JSON.stringify(comments, null, 2);
})()
```

### Method B: GitHub CLI (fallback)

Use `gh` CLI to fetch review comments:

```bash
# Extract owner/repo and PR number from the URL
# URL format: https://github.com/{owner}/{repo}/pull/{number}
gh pr view {number} --repo {owner}/{repo} --json reviews,comments
gh api repos/{owner}/{repo}/pulls/{number}/comments
gh api repos/{owner}/{repo}/pulls/{number}/reviews
```

Parse the JSON output to extract:
- File path and line numbers for each comment
- Comment body (the review suggestion)
- Comment author
- Whether it's a single comment, review comment, or part of a review thread

### Method C: GitHub MCP Tools (secondary fallback)

Use GitHub MCP tools:

```
mcp__github__get_pull_request_comments for inline review comments
mcp__github__get_pull_request_reviews for review summaries
mcp__github__get_pull_request_files for list of changed files
mcp__github__get_pull_request_diff for the full diff
```

## Step 2: Analyze Each Review Comment

For each extracted comment, determine:

1. **Is it actionable?** Skip comments that are:
   - Questions or discussions without a clear fix
   - Approvals or positive feedback ("LGTM", "looks good")
   - Already resolved/outdated comments

2. **Is the suggestion valid?** Read the relevant file and surrounding context:
   - Read the file referenced in the comment
   - Understand the broader context (function purpose, patterns used)
   - Evaluate if the suggestion improves correctness, readability, performance, or security
   - Check if the suggestion conflicts with existing project patterns or conventions

3. **Categorize the comment:**
   - **Valid fix**: The suggestion is correct and should be applied
   - **Partially valid**: The intent is correct but implementation should differ
   - **Invalid/Skip**: The suggestion is incorrect, already handled, or not applicable
   - **Needs discussion**: Requires user input before proceeding

Present the analysis to the user in a summary table before applying any fixes:

```
| # | File | Comment | Category | Reasoning |
|---|------|---------|----------|-----------|
| 1 | path/to/file.rb:42 | "Use guard clause" | Valid fix | Improves readability |
| 2 | path/to/file.rb:78 | "Add nil check" | Invalid | Already handled by L75 |
| 3 | path/to/service.rb:15 | "Extract method" | Needs discussion | Multiple approaches |
```

**Wait for user confirmation** before proceeding to apply fixes. The user may override
categorizations or skip specific comments.

## Step 3: Apply Valid Fixes

For each confirmed valid fix:

1. Read the full file to understand context
2. Apply the fix using Edit tool with precise old_string/new_string
3. Verify the fix doesn't break surrounding code
4. If the fix is complex or affects multiple files, apply changes incrementally

**Guidelines:**
- Follow existing code style and patterns in the project
- Respect project conventions from CLAUDE.md or similar config
- When a review suggests a pattern change, check if similar patterns exist elsewhere
  and only fix the specific instance mentioned (don't over-apply)
- For suggestions involving new imports/dependencies, verify they exist in the project

## Step 4: Summarize Actions

After applying all fixes, provide a summary:

```
## PR Review Fix Summary

### Applied Fixes
- [file:line] Description of change (from reviewer comment)

### Skipped Comments
- [file:line] Reason for skipping

### Needs Discussion
- [file:line] Why user input is needed

### Next Steps
- Run tests to verify changes
- Commit and push changes
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/przbadu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
