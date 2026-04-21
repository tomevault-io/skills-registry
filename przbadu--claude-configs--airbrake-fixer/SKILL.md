---
name: airbrake-fixer
description: Automatically triage and fix production Airbrake errors using Chrome browser automation. Use when the user asks to "fix airbrake errors", "check airbrake", "triage production errors", "fix production exceptions", "/airbrake", "scan airbrake", or any variation of finding and fixing errors from Airbrake. Requires Chrome browser automation (--chrome flag or chrome enabled in settings). Opens Airbrake dashboard, ranks errors by occurrence count, extracts backtrace and context, creates a git worktree, fixes the code, and creates a pull request. Use when this capability is needed.
metadata:
  author: przbadu
---

# Airbrake Fixer

Scan the Airbrake dashboard for high-occurrence production errors, extract full context (backtrace, parameters, session), fix the root cause in an isolated git worktree, and create a pull request.

## Prerequisites

- Chrome browser automation must be enabled (`--chrome` flag or chrome enabled in Claude Code settings)
- The user must be logged into Airbrake in their Chrome browser
- Git worktree support (uses the `git-worktree` skill internally)

If Chrome is not available, immediately tell the user:
> "This skill requires Chrome browser automation. Start Claude Code with `--chrome` flag or enable chrome in your settings."

## Workflow

The skill follows these steps:

1. Get the Airbrake project URL from the user
2. Open Airbrake dashboard in Chrome, filter to production + unresolved errors
3. Scrape top 5 errors ranked by occurrence count
4. Present ranked list to user and let them choose which to fix
5. Drill into the chosen error's Occurrences tab for full context
6. Find the linked GitHub issue (if any)
7. Create a git worktree for the fix
8. Fix the code based on backtrace and error context
9. Ask user for permission to create a PR
10. Commit, push, and create the pull request

---

## Step 1: Get Airbrake URL

Ask the user for their Airbrake project URL if not provided. The URL looks like:
```
https://app.airbrake.io/projects/{project_id}/groups
```

## Step 2: Open Airbrake and Filter Errors

Navigate to the Airbrake groups page. Apply filters:

1. Navigate to the provided Airbrake URL
2. Take a screenshot to see the current state
3. If not already filtered to "unresolved" status, look for a filter/status selector and set it to "unresolved"
4. Look for environment filter and set to "production" if available

See [references/airbrake-navigation.md](references/airbrake-navigation.md) for detailed Airbrake UI navigation patterns.

## Step 3: Scrape and Rank Top 5 Errors

Read the error list from the page. For each error, extract:
- **Error class and message** (e.g., `QboApi::BadRequest: [...]`)
- **Occurrence count** (the number shown, e.g., "2.2k", "248", "22")
- **Environment** label (pr, production, st)
- **Time** of last occurrence

Sort by occurrence count descending. Present the top 5 to the user in a formatted table:

```
# Top 5 Production Errors (Unresolved)

| # | Occurrences | Error | Last Seen |
|---|-------------|-------|-----------|
| 1 | 2,200 | QboApi::BadRequest: ValidationFault... | 16 hours ago |
| 2 | 570 | QboApi::Unauthorized: AUTHENTICATION... | 23 hours ago |
| 3 | 248 | StandardError: SFTP connection error... | 20 hours ago |
| 4 | 22 | ActionController::RoutingError: No such page | 17 hours ago |
| 5 | 9 | NoMethodError: undefined method 'external_user_id' | 21 hours ago |
```

Ask the user: **"Which error would you like me to fix? (enter the number)"**

## Step 4: Drill into the Error

Once the user picks an error:

1. Click on the error row to open its detail page
2. Take a screenshot of the Overview tab
3. Click on the **"Occurrences"** tab
4. Click on the most recent occurrence to see its details
5. Extract the following from the occurrence detail page:

### 4a: Get Backtrace
- Click/expand the **"Backtrace"** section
- Read the full backtrace — this tells you the exact file and line number where the error occurs
- Identify the application code lines (filter out framework/gem lines)

### 4b: Get Parameters
- Click/expand the **"Parameters"** section
- Read the request parameters — this gives context on what input caused the error

### 4c: Get Environment/Session
- Check **"Environment"** and **"Session"** sections if available
- These provide runtime context (server, user session info)

### 4d: Get Details
- Check the **"Details"** section for the full error message, URL, controller/action info

Use `get_page_text` or `read_page` to extract all this context efficiently.

## Step 5: Find Linked GitHub Issue

On the error's overview/detail page, look for the GitHub icon link. In Airbrake:
- Each error group may have a small **GitHub icon** (Octocat) next to the environment/occurrence badges
- Click this icon or hover to find the linked GitHub issue URL
- If a GitHub issue is linked, extract the issue number (e.g., `#23060`)
- If no GitHub issue is linked, note this — a new issue may need to be created

The GitHub issue number becomes the branch name: `issues/{issue_number}`

If no GitHub issue exists, use the Airbrake error ID for the branch name: `airbrake/{error_id}`

## Step 6: Create Git Worktree

Use the git-worktree skill to create an isolated worktree for the fix:

```bash
# From the main repo root
bash <git-worktree-skill-path>/scripts/create-worktree.sh {branch-name} origin/master
```

This creates a worktree at `.worktrees/{repo}-{branch-name}`.

**Important:** All subsequent code reading and editing happens in the worktree directory, not the main repo.

## Step 7: Fix the Code

Using the backtrace and error context gathered in Step 4:

1. **Locate the file** — The backtrace gives exact file paths and line numbers
2. **Read the surrounding code** — Understand the context of the error
3. **Determine the root cause** — Based on error class, message, parameters, and code
4. **Implement the fix** — Apply the minimal, correct fix

### Fix Guidelines

- **NoMethodError on nil** — Add nil checks, use `&.` safe navigation, or fix the query that returns nil
- **ActionController::RoutingError** — Check routes, add missing routes, or handle 404 properly
- **API errors (QboApi, external services)** — Add error handling, rescue blocks, retry logic
- **StandardError from external connections** — Add timeout handling, connection error rescue
- **InvalidAuthenticityToken** — Usually not a code fix; skip or add CSRF exception for API endpoints

After fixing:
- Run relevant tests if they exist for the changed files
- Make sure the fix is minimal and targeted — don't refactor surrounding code

## Step 8: Notify User and Request Permission

Present the user with a summary:

```
## Fix Summary

**Airbrake Error:** {error_class}: {error_message}
**Airbrake URL:** {link_to_error}
**GitHub Issue:** #{issue_number} (if applicable)
**Files Changed:** {list of files}
**What was fixed:** {brief description of the fix}
**Worktree:** .worktrees/{worktree-name}

Would you like me to create a pull request for this fix?
```

Wait for explicit user confirmation before proceeding.

## Step 9: Commit, Push, and Create PR

Only after user authorization:

1. **Stage and commit** the changes in the worktree:
   ```bash
   cd .worktrees/{worktree-path}
   git add {specific files}
   git commit -m "fix(scope): #{issue_number} brief description of fix"
   ```

2. **Push** the branch:
   ```bash
   git push -u origin {branch-name}
   ```

3. **Create the pull request** using `gh pr create`:
   ```bash
   gh pr create --title "fix(scope): #{issue_number} brief description" --body "## Summary
   - Fix for Airbrake error: {error_class}: {error_message}
   - {brief description of what was fixed and why}

   ## Airbrake Link
   {airbrake_error_url}

   ## Test plan
   - [ ] Verify the fix resolves the Airbrake error
   - [ ] Run existing tests for affected files
   - [ ] Monitor Airbrake after deploy for recurrence

   Closes #{issue_number}"
   ```

4. **Return the PR URL** to the user.

## Error Types to Skip

Some Airbrake errors are not fixable in code. When presenting the ranked list, note these:

- **ActionController::RoutingError for crawlers/bots** — Usually caused by bots hitting non-existent URLs
- **ActionController::InvalidAuthenticityToken** — Usually caused by expired sessions, not a code bug
- **External service connection errors** (SFTP, API timeouts) — May need infrastructure changes, not code fixes
- **Rate limiting errors** — Usually transient

Still show these to the user but note they may not be code-fixable.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/przbadu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
