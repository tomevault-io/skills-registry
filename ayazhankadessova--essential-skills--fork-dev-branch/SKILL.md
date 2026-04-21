---
name: fork-dev-branch
description: Create a development branch tied to a GitHub issue using a consistent naming convention. Use when this capability is needed.
metadata:
  author: ayazhankadessova
---

# Create a Development Branch

Sets up a new git branch for implementing a GitHub issue. The branch name follows a predictable pattern so anyone on the team can map a branch back to its issue instantly.

## Naming Convention

```
issue-<number>
```

- `<number>` is the GitHub issue number (without the `#`)
- Examples: `issue-42`, `issue-7`, `issue-138`

**Why this format?** The issue itself already holds the full context — title, description, labels, discussion. The branch name only needs to point back to it.

## Steps

### 1. Identify the Issue

Find the issue number from the conversation:
- Look for explicit mentions like "issue #42" or "implement #15"
- If the number isn't clear, list recent issues: `gh issue list --limit 10`
- If still ambiguous, ask the user which issue to target

Confirm the issue exists and is open:
```bash
gh issue view <number> --json state,title
```

If the issue is closed or missing, let the user know and stop.

### 2. Create the Branch

```bash
git checkout -b issue-<number>
```

Confirm to the user:
```
Created and switched to branch: issue-<number>
```

## Error Handling

| Problem | Response |
|---------|----------|
| Issue doesn't exist | Inform user, abort |
| Issue is closed | Inform user, abort |
| Branch name already taken | Inform user, suggest checking out the existing branch |
| No issue number provided | Ask user to specify one |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ayazhankadessova) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
