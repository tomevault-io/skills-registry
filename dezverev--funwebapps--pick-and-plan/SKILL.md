---
name: pick-and-plan
description: Picks an open GitHub issue by priority and creates an execution plan. Use when the user wants to work on issues, pick a task, plan work, or asks what to work on next. Use when this capability is needed.
metadata:
  author: dezverev
---

# Pick and Plan

Selects a prioritized GitHub issue and creates an execution plan using GitHub CLI.

## Workflow

### Step 1: Get Current Repository

Detect the current repository from git remote:

```bash
git remote get-url origin
# Extract owner/repo from URL
```

### Step 2: Fetch Open Issues

Use GitHub CLI to list open issues:

```bash
gh issue list --state open --json number,title,labels,body,createdAt,url
```

### Step 3: Filter Issues

**Ignore issues with these labels:**
- `AIIGNORE`
- `INPROGRESS`
- `DELETEME`
- `FUTUREWORK`
- `PLANCREATED`

### Step 4: Prioritize by Label

Select the highest priority issue available:

| Priority | Label | Description |
|----------|-------|-------------|
| P0 | `CONT` | Continue existing work |
| P1 | `CRITICAL` | Critical issues |
| P2 | `BUG` | Bug fixes |
| P3 | `MAINT` | Maintenance tasks |
| P4 | `DOC` | Documentation |
| P5 | `FEAT` | New features |

If multiple issues share the same priority, pick the oldest one.

### Step 5: Create Execution Plan

Analyze the selected issue and create a plan:

1. Read the issue description and comments
2. Identify the scope and requirements
3. **Check memory** for relevant context (see below)
4. **Fetch documentation** if needed (see below)
5. Break down into actionable steps
6. Estimate complexity

#### 5a: Check Memory First

Query `user-memory` for relevant knowledge:

```
search_nodes → Search for technologies/patterns mentioned in the issue
open_nodes → Get details if found
```

#### 5b: Fetch Fresh Documentation

If memory lacks info or issue involves external libraries:

```
resolve-library-id → Get the library's Context7 ID
query-docs → Fetch current documentation and patterns
```

#### 5c: Store New Knowledge

After fetching useful documentation, persist it:

```
create_entities → Create entity for the library/topic
add_observations → Store key patterns, APIs, examples
create_relations → Link to related concepts
```

**Plan Template:**

```markdown
## Execution Plan

### Summary
[One-line summary of what needs to be done]

### Steps
1. [First step]
2. [Second step]
3. [...]

### Files Likely Affected
- `path/to/file.ts`

### Testing Approach
- [How to verify the work]

### Risks/Considerations
- [Any potential issues]
```

### Step 6: Update Issue

Use GitHub CLI to update the issue body:

```bash
gh issue edit {number} --body "{original_body}

---

{plan}"
```

### Step 7: Check Branch

Before saving, ensure we're not on main:

```bash
git branch --show-current
```

**If on `main` or `master`:**

Create and switch to a new branch:

```bash
git checkout -b plan/issue-{number}-{slug}
```

**If already on a feature branch:**

Continue on current branch.

### Step 8: Save Plan to Pending

Save the plan as a markdown file in `.workplans/Pending/`:

```
.workplans/Pending/issue-{number}-{slug}.md
```

**File format:**

```markdown
# Issue #{number}: {title}

**Priority**: {priority_label}
**Created**: {date}
**GitHub**: {issue_url}

---

{full_plan_content}
```

### Step 9: Commit the Plan

After confirming the branch, commit the plan:

```bash
git add .workplans/Pending/issue-{number}-{slug}.md
git commit -m "plan(#{number}): add execution plan for {short_title}"
```

### Step 10: Add Label

Use GitHub CLI to add the `PLANCREATED` label:

```bash
gh issue edit {number} --add-label "PLANCREATED"
```

## Output

After completing, report:
- Issue number and title selected
- Priority level
- Branch name (new or existing)
- Summary of the plan created
- Plan file location (`.workplans/Pending/`)
- Commit hash
- Link to the updated issue

## Example

**User**: "What should I work on next?"

**Response**:
```
Selected Issue #42: "Fix authentication timeout"
Priority: P2 (BUG)

Created branch: plan/issue-42-fix-authentication-timeout

Plan Summary:
1. Investigate timeout in auth/session.ts
2. Add configurable timeout setting
3. Write unit tests
4. Update documentation

Plan saved: .workplans/Pending/issue-42-fix-authentication-timeout.md
Committed: plan(#42): add execution plan for fix authentication timeout

Issue updated with full plan and PLANCREATED label added.
→ https://github.com/owner/repo/issues/42
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dezverev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
