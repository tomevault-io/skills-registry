---
name: creategitlabissue
description: Create an issue in GitLab Use when this capability is needed.
metadata:
  author: capplequoppe
---

## GitLab Helper Scripts

Use these deterministic scripts instead of crafting curl commands:

| Script | Purpose |
|--------|---------|
| `.claude/hooks/gitlab/scripts/create-issue.sh` | Create a new issue |
| `.claude/hooks/gitlab/scripts/get-epic.sh <iid>` | Fetch epic details |

All scripts support `--help` for usage details.

## Steps

### 1. Analyze the description

Parse `$ARGUMENTS[0]` to understand what issue needs to be created.

### 2. Interview for details

Ask clarifying questions about:
- Acceptance criteria
- Priority and labels
- Assignee (if any)
- Related files or components

### 3. Determine epic association

Ask if the issue should be attached to an existing epic. If yes, get the epic IID.

### 4. Create the issue

Use the helper script with the template:

```bash
.claude/hooks/gitlab/scripts/create-issue.sh \
  --title="{issue-title}" \
  --description="{formatted-description}" \
  --labels="{comma-separated-labels}" \
  --epic-iid={epic-iid}  # if applicable
```

### Issue Template

Format the description as:

```markdown
## Summary

{Brief description of the issue}

## Acceptance Criteria

- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Criterion 3

## Technical Notes

{Any implementation hints or constraints}

## Related Files

- `path/to/file1`
- `path/to/file2`
```

### 5. Confirm creation

Display the created issue URL and key details to the user.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/capplequoppe) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
