---
name: create-draft-pr
description: Create a draft PR using the gh CLI Use when this capability is needed.
metadata:
  author: iainmcl
---

# create-draft-pr

Create a draft PR using the `gh` CLI.

## Check changes
First inspect the status, diff, and recent commits to understand the changes.

## Identify a PR template
Then check for a PR template in common locations:
`.github/PULL_REQUEST_TEMPLATE.md` `github/pull_request_template.md`, or
`PULL_REQUEST_TEMPLATE.md` at repo root.
If a template is found use it to base the body and fill in the summary.
If there are database migrations as part of the changes ensure that the SQL
changes even if a no-op are included in the PR description.
If the template requires a Jira link and provides a root URL, use the
root URL and find the relevant ticket number from the branch name. Git
branches are often named like `APP-107366-file-safety-validation`
where  `APP-107366` will be used for the URL - example full URL:
https://travelperk.atlassian.net/browse/APP-107366 .

## Things to include

If there are post deployment steps, include them in the PR description.
If there are any post deployment tests that need to be run, include them in the PR description.

## Things not to include

Do **not** include a description of files changed.  **Only** describe the high
level changes.  If there are points that a reviewer should pay particular
attention to.  Then after creating the draft PR, add a comment on the specific
lines explaining what should be looked at.

If outlining a test plan or equivalent do **not** include things like tests
run, tests pass, linting run. **Only** include relevant things that the
author or a reviewer will do to ensure the implementation work. Testing and
linting will be handled by CI checks.

## Frontend change screenshots

After reviewing the diff, evaluate whether the changes include **frontend/UI
modifications** (e.g. component changes, styling updates, layout shifts, new
pages or modals). If so, decide whether the PR would benefit from a screenshot
or screen recording to help reviewers understand the visual impact.

If a screenshot or recording would add value:

1. Use the **Playwright MCP** tools to launch the app and navigate to the
   affected page(s).
2. Capture a screenshot (or screen recording for interactive/animated changes).
3. The `gh` CLI **cannot** upload images to PRs. Instead:
   - Save the screenshot to `docs/screenshots/<ticket>-<description>.png`
   - Commit it to the branch and push
   - Reference it in the PR body using a blob URL:
     ```markdown
     ![Description](https://github.com/OWNER/REPO/blob/BRANCH/docs/screenshots/FILE.png?raw=true)
     ```
   - This works for private repos since the viewer is authenticated on GitHub.

Skip this step if the changes are purely backend, configuration, or
non-visual.

## Create draft PR

> **Sandbox note:** All `gh` commands require `dangerouslyDisableSandbox: true` — the sandbox breaks TLS verification for GitHub API calls. Always set this flag for every `gh` invocation.

Create a draft PR using the command:

```bash
gh pr create --draft --title \"{title}\" --body \"{body}\"
```

Return the URL.

## If the current branch isn't published

If the current branch is not published, push with `-u`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/iainmcl) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
