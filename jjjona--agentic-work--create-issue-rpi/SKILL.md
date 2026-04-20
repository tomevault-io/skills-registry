---
name: create-issue-rpi
description: Create a new issue markdown file from a template when users report problems or ask to create an issue ("i found these problems", "saw an issue", "these things are wrong", "create an issue for"). Use when an agent should capture an issue in the current project under 00-docs/issues with the required frontmatter and sections. Use when this capability is needed.
metadata:
  author: jjjona
---

# Create Issue

## Workflow

- Detect the user is reporting a problem or asking to create an issue.
- Confirm or ask for the issue title if it is not explicit.
- Resolve the target directory.
  - If `00-docs/issues/` exists in the current project, write the issue there.
  - If `00-docs/` does not exist, ask the user where to write the issue.
- Determine the owner from git config at creation time.
  - Run `git config --get user.name` in the current repo.
  - If empty, ask the user for an owner name.
- Compute the issue date in `YYYY-MM-DD`.
- Generate a slug from the issue title: lowercase, ASCII, replace spaces/punctuation with hyphens, collapse multiple hyphens, trim leading/trailing hyphens.
- Create the file as `YYYY-MM-DD-issue-slug.md` in the target directory.
- Fill the template below, using the computed date for `date` and `last_updated`, the git user for `owner`, and the confirmed issue title.

## Template

```
---

date: YYYY-MM-DD

owner: "<name>"

repository: portal

topic: "<Issue Title> — Issue Brief"

status: draft

last_updated: YYYY-MM-DD

---

# <Issue Title> — Issue Brief

## Problem

- Current behavior:

- Pain:

- Desired outcome:

## Scope

- In scope:

- Out of scope:

## UX / i18n

- Routes/screens:

- Key states (loading/empty/error):

- i18n: all strings via `dictionaries/*`

## Data / sources

- System of record:

- Read/write paths:

- Source mappings (if any):

## Rules / validation

- R1:

- R2:

## Testing

- Unit tests:

- Manual QA steps:

## Open questions

- Q1:

- Q2:
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jjjona) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
