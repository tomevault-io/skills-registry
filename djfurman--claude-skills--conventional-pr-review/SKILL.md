---
name: conventional-pr-review
description: Reviews a given PR with conventional comments focusing on good practices and providing a balanced but heavily constructive set of feedback. Use when this capability is needed.
metadata:
  author: djfurman
---

# Conventional PR Review

Review the specified PR using conventional comments format. Focus on providing constructive feedback that helps developers understand not just what to change, but **why** the suggested practice matters in their specific context.

## Getting the PR

If the user provides a PR number or URL, fetch the PR diff and details using `gh pr view <number> --json title,body,files` and `gh pr diff <number>`.

## Conventional Comments Format

Use these prefixes for all comments:

| Prefix | Usage |
|--------|-------|
| `issue:` | Something that must be fixed—bugs, security vulnerabilities, broken logic |
| `suggestion:` | Recommended improvements that would make the code better |
| `question:` | Seeking clarification about intent or approach |
| `thought:` | Sharing an observation or alternative approach worth considering |
| `nitpick:` | Minor stylistic or formatting issues (use sparingly) |
| `praise:` | Genuinely exceptional code worth highlighting (max 20% of all comments) |

### Decorators

Add these decorators in parentheses after the prefix when applicable:

- `(blocking)` - Must be resolved before merge
- `(non-blocking)` - Should be addressed but not a merge blocker
- `(if-minor)` - Only worth fixing if changes are already being made nearby

Example: `suggestion (non-blocking): Consider extracting this into a helper function...`

## Comment Structure

Every comment (except praise) must include:

1. **What**: Clearly identify the issue or suggestion
2. **Why**: Explain why this matters in the specific context—what problems it prevents, what benefits it provides, or what principle it upholds
3. **How** (when helpful): Provide a concrete example or direction for resolution

```markdown
**suggestion (non-blocking):** Extract this database query into a repository method.

Keeping data access logic in the controller couples your business logic to your persistence layer.
When you need to change how users are fetched (caching, different DB, etc.), you'd have to modify
controller code. A repository method makes this a one-line change and makes the controller testable
without a real database.
```

### Markdown Formatting

Use github flavored markdown for all comments given on a PR. Prefixes should be bolded, the comment should be introduced, and then explained.

**Good Example**

```markdown
**issue** _(blocking)_: This code block couples the implemention to the current API version.

Controller code should focus on business logic and control flow for what we need to accomplish when this API endpoint is called. The specifics of the API version should be encapuslated into a implementation function that can be swapped out without causing a change to the controller.
```

**Bad Example**

```markdown
issue (blocking): This code block couples the implemention to the current API version.

Controller code should focus on business logic and control flow for what we need to accomplish when this API endpoint is called. The specifics of the API version should be encapuslated into a implementation function that can be swapped out without causing a change to the controller.
```

## Praise Guidelines

**Maximum 20% of comments should be praise.** Reserve praise for code that:

- Demonstrates genuine mastery of a concept that's easy to get wrong
- Shows thoughtful consideration of edge cases others commonly miss
- Implements a pattern that will make future development significantly easier
- Handles a complex problem with elegant simplicity

**Do not praise:**

- Basic competency or standard practices
- Code that simply "works"
- Following formatting rules or linting requirements
- Obvious or boilerplate implementations

When praising, explain what makes it exceptional:

```markdown
**praise:** Excellent use of the null object pattern here instead of littering the codebase with null checks.
This keeps the calling code clean and makes the "no user" case explicit in the type system.
```

## Review Focus Areas

Prioritize feedback in this order:

1. **Correctness** - Logic errors, bugs, broken edge cases
2. **Security** - Vulnerabilities, data exposure, injection risks
3. **Maintainability** - Code that will cause problems for future developers
4. **Performance** - Only when there's a measurable impact
5. **Style** - Only significant deviations that harm readability

## Output Format

Generate a GitHub PR review that the user can copy and submit. The review consists of two parts:

### 1. Line Comments

Output each comment as a standalone block the user can post on the specific line in GitHub's PR interface. Group by file and include the line number for reference.

```markdown
### `src/controllers/UserController.ts`

#### Line 45-52

**issue** _(blocking)_: SQL injection vulnerability in user lookup.

The user ID is concatenated directly into the query string. An attacker could pass `1; DROP TABLE users;--` as the ID parameter. Use parameterized queries instead:

\`\`\`suggestion
const user = await db.query('SELECT * FROM users WHERE id = $1', [userId]);
\`\`\`

---

#### Line 78

**suggestion** _(non-blocking)_: Consider using early returns to reduce nesting.

Three levels of nested conditionals make this logic hard to follow. Inverting the conditions and returning early would flatten this to a linear flow, making it easier to understand what each branch does.

---
```

### 2. Review Summary

End with a summary block suitable for the PR review submission comment:

```markdown
## Review Summary

**Overall assessment:** [Approve / Request Changes / Comment]

### Key Items

- List the most critical blocking items that must be addressed
- Note any patterns (positive or concerning) observed across the PR

### Stats

- X blocking issues
- Y non-blocking suggestions
- Z other comments
```

### GitHub Suggestion Blocks

When providing code fixes, use GitHub's suggestion syntax so reviewers can apply changes with one click:

\`\`\`suggestion
// corrected code goes here
\`\`\`

This renders as an "Apply suggestion" button in GitHub's UI. Only use suggestion blocks when you can provide the exact replacement code for the selected lines.

## Review and Submission Workflow

Follow this workflow to ensure the user approves all comments before they are submitted to GitHub.

### Step 1: Generate and Present Comments

After analyzing the PR, present all comments in the output format described above. Number each comment for easy reference:

```markdown
### Comment 1 of 5

**File:** `src/controllers/UserController.ts`
**Line:** 45-52

**issue** _(blocking)_: SQL injection vulnerability...
```

### Step 2: Request User Review

After presenting all comments, ask the user to review them:

> I've generated X comments for this PR review. Please review the comments above and let me know:
> - **Approve all** - Submit all comments as-is
> - **Edit** - Specify comment numbers to modify (e.g., "Edit #3 to soften the tone")
> - **Remove** - Specify comment numbers to remove (e.g., "Remove #2 and #4")
> - **Cancel** - Abort without submitting

### Step 3: Iterate on Feedback

If the user requests changes:
1. Make the requested modifications
2. Show the updated comment(s)
3. Return to Step 2 for re-approval

### Step 4: Submit to GitHub

Once the user approves, submit the review using the GitHub MCP server:

1. **Create the PR review** using the `create_pull_request_review` tool:
   - `owner`: Repository owner
   - `repo`: Repository name
   - `pull_number`: PR number
   - `event`: `COMMENT`, `APPROVE`, or `REQUEST_CHANGES` (based on whether blocking issues exist)
   - `body`: The Review Summary content
   - `comments`: Array of line comments with `path`, `line`, and `body` fields

2. **Confirm submission** to the user with a link to the PR.

### Determining Review Event Type

- **REQUEST_CHANGES**: Use when there are any `issue (blocking)` comments
- **COMMENT**: Use when there are only non-blocking suggestions, questions, or thoughts
- **APPROVE**: Only use if the user explicitly requests approval AND there are no blocking issues

### Error Handling

If submission fails:
1. Report the error to the user
2. Offer to retry or output the comments in copy-paste format as a fallback

## Tone

Be direct and specific. Assume the developer wants to improve. Avoid:

- Softening language that obscures the message ("maybe consider perhaps...")
- Condescension or harsh criticism
- Vague feedback ("this could be better")

Focus on teaching—every piece of feedback is an opportunity to help someone become a better developer.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/djfurman) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
