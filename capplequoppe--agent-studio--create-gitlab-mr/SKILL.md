---
name: creategitlabmergerequest
description: Create a merge request in GitLab Use when this capability is needed.
metadata:
  author: capplequoppe
---

## GitLab Helper Scripts

Use these deterministic scripts instead of crafting curl commands:

| Script | Purpose |
|--------|---------|
| `.claude/hooks/gitlab/scripts/create-mr.sh` | Create a new MR |

Run `.claude/hooks/gitlab/scripts/create-mr.sh --help` for all options.

## Steps

### 1. Verify branch state

```bash
# Ensure current branch is pushed
git push -u origin HEAD

# Get current branch name
git branch --show-current
```

### 2. Determine target branch

Check if working on:
- An execution plan phase → target is `phase/{plan}/{phase}`
- An epic → target is `epic/{epic-iid}-{slug}`
- Standalone → target is `master`

Use git log or branch name patterns to determine this.

### 3. Ensure target is up to date

```bash
git fetch origin
git merge origin/{target-branch} --no-edit
```

Resolve any conflicts if present.

### 4. Analyze changes

```bash
git log origin/{target-branch}..HEAD --oneline
git diff origin/{target-branch}..HEAD --stat
```

### 5. Formulate MR description

Create a summary that includes:
- Purpose of the changes
- Key modifications
- Testing performed
- Any breaking changes

### 6. Create the merge request

```bash
.claude/hooks/gitlab/scripts/create-mr.sh \
  --source="$(git branch --show-current)" \
  --target="{target-branch}" \
  --title="{descriptive-title}" \
  --description="## Summary

{description}

## Changes

{list of key changes}

## Testing

{testing notes}
"
```

### 7. Report result

Display the created MR URL and key details to the user.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/capplequoppe) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
