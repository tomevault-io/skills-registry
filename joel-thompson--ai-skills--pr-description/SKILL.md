---
name: pr-description
description: Write a PR description for the current branch using local git and GitHub MCP. Use when the user asks for a PR description, wants to draft PR text, or needs to document branch changes. Triggers on: pr description, write pr description, draft pr, describe my changes. Use when this capability is needed.
metadata:
  author: joel-thompson
---

# PR Description

Generate a structured PR description from the current branch's changes. Return it inline in a copyable markdown code block—do not create a file.

## Strict rules

- Never create a file; return markdown inline so the user can click the copy button.
- The PR description must be inside a markdown code fence so it is copyable.
- Use local git as the source of truth for changes; GitHub MCP is supplementary.
- If GitHub MCP calls fail, continue with local git data only (best-effort).
- Do not include branch name or commit message suggestions.

## Inputs

The user runs this from a git repository with uncommitted or committed changes on a branch. No explicit input is required; the skill infers context from the current working directory.

## Workflow (do this in order)

1. **Local git context (required)**
   - Current branch: `git branch --show-current`
   - Base branch: `main` or `master` (use whatever exists)
   - Commit history: `git log <base>..HEAD --oneline`
   - File summary: `git diff <base>...HEAD --stat`
   - Full diff: `git diff <base>...HEAD` (for analysis)
   - If the repo is not a git repo or has no changes, state this and stop.

2. **GitHub MCP context (best-effort)**
   - Parse `git remote get-url origin` to extract `owner` and `repo`.
   - Call `mcp_github_list_pull_requests` with `head` filter: `owner:current-branch` to see if a PR already exists for this branch.
   - If a PR exists, call `mcp_github_get_pull_request` to fetch its title and description.
   - Use the existing description to refine or update rather than generate from scratch.
   - If MCP calls fail or the remote is not GitHub, proceed with local git data only.

3. **Analyze and generate**
   - Summarize the diff and commits into logical changes.
   - Group changes by area (e.g., backend, frontend, tests), not per-file.
   - Produce a structured description with Summary, Changes, and Files Changed.
   - If an existing PR description was fetched, update it to reflect the latest diff rather than fully replacing it when appropriate.

## PR description structure

The generated description must include:

- **Summary** — 2–3 sentences explaining what changed and why.
- **Changes** — Bulleted list of logical changes, grouped by area.
- **Files Changed** — List of modified files with brief annotations where helpful.

## Output

Return the PR description inside a markdown code block. No file creation. Precede it with "PR Description:" so the user knows what to copy.

Example structure:

PR Description:

```markdown
# Summary

This PR adds training goals to the tracker. Users can now set goals and assign them to categories.

# Changes

- Added goals table and migrations
- Added goals API endpoint with CRUD operations
- Added goals UI component with list and form views

# Files Changed

- `src/db/schema.ts` — goals table definition
- `src/app/api/goals/route.ts` — API handler
- `src/app/features/training/goals/goals.component.ts` — UI component
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/joel-thompson) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
