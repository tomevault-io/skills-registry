---
name: generate-git-labels
description: Creates required GitHub labels for issue tracking and workflow automation. Use when setting up a new repository, when labels are missing, or when the user asks to add or sync project labels.
metadata:
  author: dezverev
---

# Generate Git Labels

Ensures all required labels exist in the current GitHub repository for the pick-and-plan workflow.

## Required Labels

### Priority Labels

| Label | Color | Description |
|-------|-------|-------------|
| `CONT` | `#7057ff` | P0: Continue existing work |
| `CRITICAL` | `#d73a4a` | P1: Critical issues |
| `BUG` | `#d73a4a` | P2: Bug fixes |
| `MAINT` | `#0075ca` | P3: Maintenance tasks |
| `DOC` | `#0075ca` | P4: Documentation |
| `FEAT` | `#a2eeef` | P5: New features |

### Workflow Labels

| Label | Color | Description |
|-------|-------|-------------|
| `AIIGNORE` | `#eeeeee` | AI should ignore this issue |
| `INPROGRESS` | `#fbca04` | Work in progress |
| `DELETEME` | `#eeeeee` | Marked for deletion |
| `FUTUREWORK` | `#d4c5f9` | Deferred to future |
| `PLANCREATED` | `#c5def5` | Execution plan created |

## Workflow

### Step 1: Verify Repository

Confirm you're in a git repository with a GitHub remote:

```bash
gh repo view --json name,owner
```

### Step 2: Get Existing Labels

List current labels to avoid duplicates:

```bash
gh label list --json name
```

### Step 3: Create Missing Labels

For each missing label, run:

```bash
gh label create "LABEL_NAME" --color "COLOR" --description "DESCRIPTION"
```

**Priority Labels:**
```bash
gh label create "CONT" --color "7057ff" --description "P0: Continue existing work"
gh label create "CRITICAL" --color "d73a4a" --description "P1: Critical issues"
gh label create "BUG" --color "d73a4a" --description "P2: Bug fixes"
gh label create "MAINT" --color "0075ca" --description "P3: Maintenance tasks"
gh label create "DOC" --color "0075ca" --description "P4: Documentation"
gh label create "FEAT" --color "a2eeef" --description "P5: New features"
```

**Workflow Labels:**
```bash
gh label create "AIIGNORE" --color "eeeeee" --description "AI should ignore this issue"
gh label create "INPROGRESS" --color "fbca04" --description "Work in progress"
gh label create "DELETEME" --color "eeeeee" --description "Marked for deletion"
gh label create "FUTUREWORK" --color "d4c5f9" --description "Deferred to future"
gh label create "PLANCREATED" --color "c5def5" --description "Execution plan created"
```

### Step 4: Report Results

List all labels to confirm:

```bash
gh label list
```

## Output

Report:
- Labels that already existed (skipped)
- Labels that were created
- Any errors encountered

## Example

```
Checking labels in owner/repo...

Existing labels: bug, enhancement, documentation
Missing labels: CONT, CRITICAL, BUG, MAINT, DOC, FEAT, AIIGNORE, INPROGRESS, DELETEME, FUTUREWORK, PLANCREATED

Creating labels...
✓ CONT (P0: Continue existing work)
✓ CRITICAL (P1: Critical issues)
✓ BUG (P2: Bug fixes)
✓ MAINT (P3: Maintenance tasks)
✓ DOC (P4: Documentation)
✓ FEAT (P5: New features)
✓ AIIGNORE (AI should ignore this issue)
✓ INPROGRESS (Work in progress)
✓ DELETEME (Marked for deletion)
✓ FUTUREWORK (Deferred to future)
✓ PLANCREATED (Execution plan created)

Done! 11 labels created.
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dezverev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
