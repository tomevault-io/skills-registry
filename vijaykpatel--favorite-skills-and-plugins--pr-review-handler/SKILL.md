---
name: pr-review-handler
description: Handle PR review feedback by checking for comments, determining necessary changes, responding appropriately to reviewers, implementing fixes, and pushing updates. Use when the user asks to work on an existing open PR, address PR feedback, fix review comments, or handle bot review suggestions (Devin AI, Cursor Bugbot, etc.). Use when this capability is needed.
metadata:
  author: vijaykpatel
---

# PR Review Handler

Handle pull request review feedback systematically: fetch comments, analyze feedback, implement fixes, respond to reviewers, and push updates.

## When to Use

Use this skill when:
- User asks to "work on PR #X" or "address PR feedback"
- User provides a PR URL and asks to fix review comments
- User asks to "respond to review comments" or "fix the PR issues"
- Bot reviewers (Devin AI, Cursor Bugbot) have left feedback
- Multiple reviewers have left comments requiring coordination

## Workflow

### 1. Fetch PR Information

Get the PR number from the user or URL, then fetch details:

```bash
# Get PR metadata
gh pr view <number> --json number,title,state,baseRefName,headRefName,author,url

# Get reviews and their state
gh pr view <number> --json reviews --jq '.reviews[] | "Review by \(.author.login) - \(.state):\n\(.body)\n---"'

# Get review comments (line-specific feedback)
gh api repos/<owner>/<repo>/pulls/<number>/comments --jq '.[] | "File: \(.path)\nLine: \(.line // .original_line)\nComment by \(.user.login):\n\(.body)\n---"'
```

### 2. Analyze Feedback

For each comment, categorize by:

**Priority:**
- Critical: Security issues, bugs that cause crashes, data loss
- High: Bugs, performance issues, incorrect implementations
- Medium: Code quality, refactoring suggestions, best practices
- Low: Style issues, naming suggestions, optional improvements

**Actionability:**
- Actionable: Clear change requested with specific fix
- Question: Reviewer asking for clarification
- Discussion: Design discussion or trade-off debate
- Informational: FYI comment, no action needed

**Bot vs Human:**
- Bot comments (Devin AI, Cursor Bugbot): Often auto-fixable, may have suggested code
- Human comments: May require judgment, discussion, or clarification

### 3. Plan Changes

Create a prioritized list of changes:

1. Group related comments (e.g., all comments about validation)
2. Identify dependencies (Fix A must happen before Fix B)
3. Separate code changes from response-only comments
4. Note which comments need discussion vs implementation

### 4. Checkout Branch

```bash
git fetch origin
git checkout <head-branch-name>
git pull origin <head-branch-name>
```

### 5. Implement Fixes

Work through changes by priority:

**For each fix:**
1. Read relevant files
2. Implement the change
3. Verify the fix (run tests, build, etc.)
4. Stage changes: `git add <files>`

**Commit strategy:**
- Single fix commit: If all changes are tightly related
- Multiple commits: If addressing distinct concerns (validation, refactoring, bug fix)
- Use clear commit messages: "Fix: <what> per <reviewer> feedback"

### 6. Respond to Comments

Respond to each comment appropriately:

**For implemented fixes:**
```bash
gh api -X POST repos/<owner>/<repo>/pulls/<number>/comments/<comment-id>/replies \
  -f body="Fixed in <commit-sha>. <Brief explanation of fix>"
```

**For questions/clarifications:**
```bash
gh api -X POST repos/<owner>/<repo>/pulls/<number>/comments/<comment-id>/replies \
  -f body="<Clear answer to question>"
```

**For won't-fix with reasoning:**
```bash
gh api -X POST repos/<owner>/<repo>/pulls/<number>/comments/<comment-id>/replies \
  -f body="<Explanation of why not changing, with reasoning>"
```

**Response guidelines:**
- Be concise: 1-3 sentences
- Reference commit SHAs for fixes
- Explain reasoning for design decisions
- Thank reviewers for catching issues
- Ask follow-up questions if unclear

### 7. Push Changes

```bash
git push origin <head-branch-name>
```

### 8. Verify and Summarize

After pushing:

```bash
# Check CI status
gh pr checks <number>

# View updated PR
gh pr view <number>
```

Provide user with summary:
- Number of comments addressed
- Commits pushed
- Comments that need discussion
- Any blockers or questions

## Common Patterns

### Pattern 1: Bot Review Fixes

Bot reviews (Devin AI, Cursor Bugbot) often suggest specific code:

1. Fetch comments
2. For each bot suggestion:
   - Assess validity (is this actually an issue?)
   - Implement fix if valid
   - Respond with "Fixed in <sha>" or explain why not applicable
3. Group related bot fixes into single commit
4. Push once after addressing all bot feedback

### Pattern 2: Mixed Human + Bot Reviews

1. Address critical human feedback first
2. Handle bot feedback second
3. Respond to human questions/discussions
4. Push changes
5. Respond to all comments with commit references

### Pattern 3: Conflicting Feedback

When reviewers disagree:

1. Acknowledge both perspectives in responses
2. Make a reasoned decision based on project context
3. Explain decision to both reviewers
4. Offer to discuss synchronously if needed

### Pattern 4: Large Refactoring Requests

When reviewer requests significant refactoring:

1. Assess scope (does this belong in this PR?)
2. If in-scope: Implement and push
3. If out-of-scope: Respond proposing separate PR/issue
4. If unclear: Ask reviewer to clarify scope expectations

## Response Templates

**Fixed:**
```
Fixed in <sha>. <One sentence explaining what changed>
```

**Won't fix with reason:**
```
Keeping as-is because <reasoning>. <Alternative if applicable>
```

**Clarification question:**
```
<Answer to question>. <Additional context if helpful>
```

**Agreement to refactor separately:**
```
Good point. I'll address this in a follow-up PR to keep this change focused on <original scope>.
```

**Design decision explanation:**
```
I chose <approach> over <alternative> because <reasoning>. Open to changing if you feel strongly.
```

## Best Practices

- **Read before responding**: Always fetch and read all comments before starting fixes
- **Commit atomically**: Each commit should address a coherent set of changes
- **Respond to everything**: Even "Fixed in <sha>" is better than silence
- **Be professional**: Thank reviewers, explain decisions, admit mistakes
- **Verify before pushing**: Run tests/build to avoid pushing broken code
- **Update PR description**: If changes significantly alter the PR, update description
- **Re-request review**: Use `gh pr ready <number>` if PR was marked as draft

## Error Handling

**If gh command fails:**
- Check if gh is installed: `gh --version`
- Check if authenticated: `gh auth status`
- Check if PR number is valid

**If git push fails:**
- Check for conflicts: `git status`
- Pull latest: `git pull origin <branch> --rebase`
- Resolve conflicts if needed

**If unable to implement fix:**
- Respond to comment explaining blocker
- Ask reviewer for guidance
- Don't leave comment unanswered

## Examples

See [references/examples.md](references/examples.md) for complete examples of handling different review scenarios.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vijaykpatel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
