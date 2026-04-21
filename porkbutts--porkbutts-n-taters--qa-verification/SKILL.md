---
name: qa-verification
description: QA verification skill that tests like a human QA would — navigating the app in a browser, looking at what's on screen, and flagging anything that looks wrong. Use when verifying implemented features match task specifications, testing user flows, or validating acceptance criteria from docs/TASKS.md. Triggers on "verify this PR", "QA this feature", "visual test", "check the UI", "screenshot test", "verify task X". Works against localhost, preview/staging deployments, and production URLs. Auto-selects Google accounts; pauses for user assistance only on credential-based login flows. Use when this capability is needed.
metadata:
  author: porkbutts
---

# QA Verification

Test like a human QA — navigate the app, look at the screen, and flag what looks wrong.

## Core Rules

1. **Screenshots are your eyes.** Every verification judgment must come from looking at a screenshot. If you can't see it in the screenshot, you can't claim it passes or fails.

2. **`browser_snapshot` is for interaction only.** You need snapshot refs to click buttons and fill forms — that's fine. But NEVER use snapshot/DOM data to verify acceptance criteria. A human QA doesn't open DevTools to check if a element exists; they look at the screen.

3. **Flag visual problems even if they aren't in the acceptance criteria.** A human QA notices when a modal is cut off, text overflows its container, buttons overlap, or layout looks broken. You should too. Report these as additional findings separate from acceptance criteria results. **Major visual issues FAIL the PR** — see severity rules below.

5. **Don't rationalize away visual bugs.** If a modal is unreadable, a layout is broken, or UI is unusable, that's a FAIL — even if every acceptance criterion technically passes. Never downgrade a major visual issue to "non-blocking" or "note it for later". If a user would look at the screen and say "this is broken", the PR fails.

4. **Navigate like a user.** Click links, fill forms, wait for pages to load. Don't skip steps or assume state.

## What to Look For

Beyond acceptance criteria, flag anything a human would notice:

- **Layout issues** — elements overlapping, content cut off at viewport edges, modals extending off-screen, unexpected scrollbars
- **Text problems** — truncated labels, text overflowing containers, unreadable contrast, placeholder text still showing
- **Broken interactions** — buttons that don't appear clickable, missing hover/focus states, forms that don't respond
- **Loading/state issues** — spinners that never resolve, flash of unstyled content, empty states where data should be
- **Visual inconsistencies** — misaligned elements, inconsistent spacing, elements that look out of place

### Visual Issue Severity

Every visual issue must be classified:

| Severity | Meaning | Effect on Verdict |
|----------|---------|-------------------|
| **Minor** | Cosmetic nits — slightly off spacing, alignment, minor inconsistencies | Note in report, does NOT affect verdict |
| **Major** | Unusable UI — content unreadable, overlapping elements that block interaction, broken modals, missing backdrops that make dialogs illegible | **FAIL the PR**, same as a failed acceptance criterion |

**When in doubt, ask: "Would a user be able to complete this task?"** If the answer is no, it's Major.

## Workflow

### 1. Parse Task Specification

Read the task file from `docs/tasks/task-<id>.md` or `docs/TASKS.md`. Extract:

- **Acceptance criteria**: Specific conditions to verify
- **User flows**: Step-by-step interactions to walk through

If user specifies a PR number, use `gh pr view <number>` to identify affected files and infer the relevant task.

### 2. Gather Test Context

Before starting, collect:

| Item | Source | Default |
|------|--------|---------|
| **Target URL** | User-provided | `http://localhost:3000` (also works with preview/staging/production URLs) |
| **Task/PR** | User-specified | Ask user |
| **Auth required?** | Task spec or inference | Assume no |
| **Screenshot dir** | User preference | `./qa-screenshots/` |

### 3. Clean Screenshot Directory

Before taking any screenshots, wipe the screenshot directory to remove leftovers from previous runs:

```bash
rm -rf qa-screenshots/ && mkdir -p qa-screenshots/
```

### 4. Verification Loop

For each acceptance criterion:

```
1. SCREENSHOT the current state
2. LOOK at the screenshot — does everything look right?
3. ACT — click, type, navigate (use browser_snapshot only to get element refs)
4. WAIT for the page to settle
5. SCREENSHOT the result
6. LOOK at the screenshot — did the expected thing happen? Does anything look off?
7. RECORD the result with screenshot references
```

At every screenshot, ask yourself: "If I were a human looking at this screen, would anything catch my eye as wrong?" Flag it even if it's unrelated to the current criterion.

### 5. Authentication Handling

**Google Account Selection — handle automatically:**

If the page shows a Google account picker ("Choose an account", list of emails), click the first account and continue. Do NOT ask the user for help.

```
1. browser_take_screenshot to see the auth screen
2. If it looks like a Google account picker, browser_snapshot to get the ref
3. browser_click the first account
4. browser_wait_for(time=2) for redirect
5. browser_take_screenshot to confirm auth completed
6. Continue verification
```

**Credential-based Login — defer to user:**

If the page has a username/password form, pause and ask:

```markdown
## Auth Required

I've encountered a login screen at [URL].

**Screenshot:** auth-required.png

**Options:**
1. **Manual login**: I'll wait while you log in via the browser, then continue
2. **Skip auth flows**: Mark auth-required tests as SKIPPED
3. **Provide credentials**: Share test credentials to proceed
```

### 6. Screenshot Strategy

| When | Filename Pattern | Why |
|------|------------------|-----|
| Before any action | `{criterion}-before-{action}.png` | Baseline |
| After action completes | `{criterion}-after-{action}.png` | Verify result |
| Something looks wrong | `{criterion}-FAIL-{desc}.png` | Evidence |
| Visual issue unrelated to criteria | `visual-issue-{desc}.png` | Additional finding |

- Use `fullPage: true` for layout verification
- Use element screenshots for component-level detail
- PNG format

### 7. Post Screenshots & Report to PR

After completing all verifications, if a PR number is known:

**Step A: Commit screenshots to the PR branch.**

```bash
# Ensure you're on the PR branch
git checkout <branch>

# Stage screenshots
git add qa-screenshots/

# Commit
git commit -m "QA screenshots for PR #<number>"

# Push to remote
git push
```

**Step B: Build image URLs and post the report as a PR comment.**

Construct image URLs using the format:
```
https://github.com/<owner>/<repo>/blob/<branch>/qa-screenshots/<filename>?raw=true
```

Get owner/repo from:
```bash
gh repo view --json nameWithOwner --jq '.nameWithOwner'
```

Embed screenshots inline in the report using markdown image syntax:
```markdown
**Screenshot:** ![description](https://github.com/<owner>/<repo>/blob/<branch>/qa-screenshots/<filename>?raw=true)
```

Post the report as a PR comment:
```bash
gh pr comment <number> --body "<report with embedded images>"
```

Use a HEREDOC to pass the report body to avoid shell escaping issues.

If no PR number is known, skip posting and just output the report.

### 8. Generate Report

```markdown
# QA Report

**Task:** [task ID or PR number]
**URL:** [target URL]
**Date:** [timestamp]
**Screenshots:** [directory path]

## Summary
- Passed: X
- Failed: Y
- Skipped: Z
- Visual Issues: N minor, M major
- **Verdict: PASS / FAIL**

## Acceptance Criteria Results

### 1. [Criterion]
**Status:** PASS / FAIL / SKIP
**Steps:**
1. [What you did]
   ![before](https://github.com/<owner>/<repo>/blob/<branch>/qa-screenshots/<before-screenshot>.png?raw=true)
2. [What you saw]
   ![after](https://github.com/<owner>/<repo>/blob/<branch>/qa-screenshots/<after-screenshot>.png?raw=true)
**Notes:** [What you verified visually]

## Additional Visual Issues

### [Issue description]
![issue](https://github.com/<owner>/<repo>/blob/<branch>/qa-screenshots/<issue-screenshot>.png?raw=true)
**Severity:** Minor / Major
**Details:** [What looks wrong and where]
```

### Verdict Rules

**PASS** when:
- All acceptance criteria pass
- No Major visual issues

**FAIL** when ANY of:
- Any acceptance criterion fails
- Any Major visual issue found (unusable UI, unreadable content, broken interactions)

A PR with all acceptance criteria passing but a Major visual issue is a **FAIL**. Do not pass it with a note — fail it and explain what needs fixing.

## Playwright Tools

**For interaction** (getting refs, clicking, typing):
- `browser_snapshot` — get element refs so you can interact. NOT for verification.
- `browser_click`, `browser_type`, `browser_fill_form`, `browser_select_option` — interact with the page
- `browser_navigate` — go to URLs
- `browser_wait_for` — wait for page updates

**For verification** (looking at the screen):
- `browser_take_screenshot` — this is how you see. Use it constantly.

## Example Session

**User:** "Verify task 3.1 against localhost:5173"

1. Read `docs/tasks/task-3.1.md`, extract acceptance criteria
2. `browser_navigate` to `http://localhost:5173`
3. `browser_take_screenshot` — look at the landing state, note anything off
4. For each criterion:
   - Screenshot before
   - `browser_snapshot` to get refs, then interact
   - Wait for result
   - Screenshot after
   - **Look at the screenshot** — does it pass? Anything else look wrong?
   - Record result
5. If auth encountered: auto-select Google account or pause for credential login
6. Generate report with all screenshots, criteria results, and any additional visual issues found

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/porkbutts) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
