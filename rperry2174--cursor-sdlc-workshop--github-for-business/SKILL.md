---
name: github-for-business
description: Explains Git/GitHub actions in plain business language after GitHub-related work completes. Use when running or discussing git/gh commands, branches, pull requests, reviews, merges, conflicts, or pushing to origin. Use when this capability is needed.
metadata:
  author: rperry2174
---

# GitHub for Business (Plain-English Narration)

## When to use

Use this whenever the work includes Git or GitHub actions (examples: `git checkout -b`, `git add`, `git commit`, `git push`, `gh pr create`, PR review/merge, resolving merge conflicts).

## Core behavior

After completing Git/GitHub commands, add a short “what just happened” explanation for a non-technical audience.

Keep it:
- short (3–8 bullets)
- concrete (tie to the actual commands that ran)
- analogy-driven (Google Docs / shared folder / change request)

## Preferred analogy (default)

Use a **Google Docs** analogy unless the context clearly fits better with another analogy.

- **repo**: shared Google Drive folder for the project
- **main branch**: the “official” document everyone relies on
- **branch**: your private copy/draft version
- **commit**: a saved checkpoint with a note (“what changed and why”)
- **push**: uploading your draft/checkpoints to the shared Drive so others can see it
- **pull request (PR)**: “Request approval to apply my changes to the official version”
- **review**: teammates reading/commenting before accepting
- **merge**: accepting the PR so the official version includes the changes
- **merge conflict**: two people edited the same sentence differently; you must choose the final wording

## Map common commands to meaning

When relevant, translate the exact commands that ran:

- `git status`: “What have I changed locally? What’s ready to share?”
- `git diff`: “Show me exactly what edits I made.”
- `git checkout -b X` / `git switch -c X`: “Create a safe sandbox draft (branch) named X.”
- `git add <files>`: “Choose which edits go into the ‘shareable package’.”
- `git commit -m "..."`: “Seal the package with a label describing the change.”
- `git push origin X` / `git push -u origin X`: “Upload the package + connect this branch to GitHub.”
- `gh pr create`: “Open the approval request (PR) on GitHub.”
- `gh pr view` / `gh pr checks`: “See the PR and whether automated checks passed.”
- `gh pr merge`: “Finalize by accepting the change into the official version.”

## Output template (append at end)

Add a short section at the end of the response:

### What happened (plain English)
- **Big picture**: …
- **In Google Docs terms**: …
- **Why this matters**: …
- **What to expect next** (optional): …

## Example

If the agent ran:
- `git checkout -b team-1/adds-search`
- `git add .`
- `git commit -m "Add search"`
- `git push -u origin team-1/adds-search`
- `gh pr create ...`

Then narrate:

### What happened (plain English)
- **Big picture**: We created a safe sandbox, packaged our edits, uploaded them to GitHub, and opened a request to add them to the official code.
- **In Google Docs terms**: You made a copy of the doc, saved your edits with a label, shared that copy to the team’s Drive, and clicked “Request review” so someone can approve merging into the main doc.
- **Why this matters**: It prevents accidental breaking changes and makes teamwork auditable and reviewable.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rperry2174) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
