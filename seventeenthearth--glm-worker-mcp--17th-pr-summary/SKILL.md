---
name: 17th-pr-summary
description: | Use when this capability is needed.
metadata:
  author: seventeenthearth
---

# PR Summary

Generate PR title and summary following project conventions.

## When to Use

- Before creating a PR
- After completing implementation work
- When you need consistent PR formatting

## Project Conventions

### Title Format

```
[TAG] Brief description in imperative mood
```

**Tag Derivation:**
- Derive from branch name, converted to UPPERCASE
- If branch is `main`, use `INT`

| Branch | Tag |
|--------|-----|
| `task-177` | `[TASK-177]` |
| `ctask-105` | `[CTASK-105]` |
| `ui-ves-42` | `[UI-VES-42]` |
| `fix-auth-bug` | `[FIX-AUTH-BUG]` |
| `main` | `[INT]` |

**Examples:**
- `[TASK-177] Transition external contracts to profile_id-only`
- `[CTASK-105] Update client SDK for profile identity`
- `[UI-VES-42] Redesign space participant list`
- `[INT] Update docs`

### Body Format

Single paragraph describing:
1. What this PR does (main change)
2. Key implementation details
3. Impact/benefits

**Example:**
```
This PR completes the transition to a profile-only identity model by removing
the PROFILE_IDENTITY_MODE configuration toggle and all associated legacy
fallback logic. It eliminates dual-writing of profile metadata to the users
table and refactors read-side SQL queries to treat the profiles table as the
sole source of truth. These changes significantly reduce architectural
complexity and establish a consistent state for upcoming schema cleanups.
```

## Usage

```
/pr-summary                    → Analyze staged changes
/pr-summary task-177           → Use TASK-177 as prefix
/pr-summary --branch           → Analyze all commits on current branch vs main
```

## Implementation

### 1. Detect Context

```bash
# Get current branch
branch=$(git branch --show-current)

# Derive tag from branch name
if [ "$branch" = "main" ]; then
    tag="INT"
else
    # Convert branch name to uppercase
    tag=$(echo "$branch" | tr '[:lower:]' '[:upper:]')
fi

# Get base branch
base_branch="main"
```

**Tag Examples:**
- `task-177` → `TASK-177`
- `ctask-105` → `CTASK-105`
- `ui-ves-42` → `UI-VES-42`
- `main` → `INT`

### 2. Gather Changes

```bash
# Option A: Staged changes
git diff --cached --stat
git diff --cached

# Option B: Branch changes (all commits since base)
git log --oneline ${base_branch}..HEAD
git diff ${base_branch}...HEAD --stat
```

### 3. Analyze with GLM

```python
submit_task(
    task="""
Analyze the following git changes and generate a PR title and summary.

## Branch Info
- Current branch: {branch_name}
- Tag: [{tag}]
- Base branch: {base_branch}

## Changes
{diff_output}

## Commit History (if branch mode)
{commit_log}

## Instructions

1. **Title**:
   - Format: [{tag}] Brief description
   - Use imperative mood (Add, Remove, Update, Fix, Implement)
   - Keep under 72 characters
   - Be specific but concise

2. **Summary**:
   - Single paragraph
   - Start with "This PR..."
   - Describe main changes
   - Mention key implementation details
   - Note impact/benefits
   - Professional tone

3. **Output Format**:
```
## PR Title
{title}

## PR Summary
{summary}
```

Generate the PR title and summary now.
""",
    working_dir=project_root,
    max_iterations=100
)
```

### 4. Present to User

Display:
- Suggested title
- Suggested summary
- Option to copy or adjust

## Examples

### From Staged Changes

```
User: /pr-summary

Claude:
Analyzing staged changes...

## Suggested PR

**Title:**
[TASK-177] Transition Space/Friend/QuizResult contracts to profile_id-only

**Summary:**
This PR completes the profile identity transition by removing user_id from
external gRPC contracts in Space, Friend, and QuizResult domains. It updates
StreamSpace patch paths to use profile_id, modifies resume_token format to
v1:{space_id}:{version}:{profile_id}, and adds new GIN index for JSONB
participant queries. The comparison favorites and memo APIs now require
profile_id as mandatory input with ownership validation.

---
Ready to create PR? Run: gh pr create --title "..." --body "..."
```

### From Branch

```
User: /pr-summary --branch

Claude:
Analyzing 12 commits on task-177 vs main...

(same output format)
```

## Configuration

| Parameter | Default | Description |
|-----------|---------|-------------|
| base_branch | main | Branch to compare against |
| max_diff_lines | 500 | Truncate large diffs |

## Output Location

If requested, save to: `.kkachi/{branch_name}/completed/pr-summary.md`

## Notes

- Uses GLM-5 for analysis (fast, cost-effective)
- Does NOT create the PR (user handles that)
- Follows existing repo conventions automatically
- Can regenerate if user wants adjustments

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/seventeenthearth) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
