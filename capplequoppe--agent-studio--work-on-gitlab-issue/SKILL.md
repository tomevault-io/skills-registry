---
name: workongitlabissue
description: Work on an issue from GitLab Use when this capability is needed.
metadata:
  author: capplequoppe
---

## GitLab Helper Scripts

Use these deterministic scripts instead of crafting curl commands:

| Script | Purpose |
|--------|---------|
| `.claude/hooks/gitlab/scripts/get-issue.sh <iid>` | Fetch issue details |
| `.claude/hooks/gitlab/scripts/get-epic.sh <iid>` | Fetch epic details |
| `.claude/hooks/gitlab/scripts/create-mr.sh` | Create merge request |

All scripts support `--help` for usage details.

## Steps

### 1. Review pre-fetched context

The hook has already fetched issue details and calculated the branch strategy. Review the context above for:
- Issue title, description, and labels
- Epic association (if any)
- Recommended branch names and MR target

### 2. Check out or create branches

Based on the branch strategy from the context:

**For execution plans** (Case A):
```bash
git fetch origin
git checkout plan/{plan-name} 2>/dev/null || git checkout -b plan/{plan-name} origin/master
git checkout phase/{plan-name}/{phase-name} 2>/dev/null || git checkout -b phase/{plan-name}/{phase-name}
git checkout -b feat/{iid}-{slug}
```

**For epics** (Case B):
```bash
git fetch origin
git checkout epic/{epic-iid}-{slug} 2>/dev/null || git checkout -b epic/{epic-iid}-{slug} origin/master
git checkout -b feat/{iid}-{slug}
```

**For standalone issues** (Case C):
```bash
git fetch origin
git checkout -b feat/{iid}-{slug} origin/master
```

If the issue already has an MR, check out that branch instead.

### 3. Analyze requirements

- Extract acceptance criteria from the issue description
- If the issue belongs to an epic, review the epic description for additional context
- Explore the codebase as needed

### 4. Interview the user

Ask clarifying questions about requirements, edge cases, or implementation choices before writing code.

### 5. Implement the solution

Make code changes following Clean Code Best Practices and SOLID Principles.

### 6. Test

Run tests related to the issue using a subagent.

### 7. Debug if needed

Fix any failing tests.

### 8. Code review

Review code changes for quality and adherence to best practices.

### 9. Commit

Prepare a commit message summarizing the changes. Do NOT push without user approval.

### 10. Create a merge request

Use the helper script:
```bash
.claude/hooks/gitlab/scripts/create-mr.sh \
  --source="feat/{iid}-{slug}" \
  --target="{mr-target-from-strategy}" \
  --title="Resolve issue #{iid}: {title}" \
  --description="Closes #{iid}

## Summary
{summary of changes}"
```

Do NOT merge without user approval.

## Branch Naming Reference

```
Case A — Multi-phase execution plan:
  master
    └── plan/{plan-name}
          ├── phase/{plan-name}/{phase-name-1}
          │     └── feat/{iid}-{slug}
          └── phase/{plan-name}/{phase-name-2}

Case B — Single epic:
  master
    └── epic/{epic-iid}-{epic-slug}
          └── feat/{iid}-{slug}

Case C — Standalone issue:
  master
    └── feat/{iid}-{slug}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/capplequoppe) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
