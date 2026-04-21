---
name: open-issue
description: Draft and create GitHub issues from conversation context with structured formatting and tag selection. Use when this capability is needed.
metadata:
  author: ayazhankadessova
---

# Create a GitHub Issue

Extracts context from the current conversation, drafts a well-structured GitHub issue, and creates it after user confirmation.

## Issue Structure

```markdown
# [tag]: Brief summary of the issue

## Description

What the issue is about — the problem, the affected area, and why it matters.

## Steps to Reproduce

(For bug reports only)
Minimal steps to trigger the problem.

## Proposed Solution

(Optional — include when an implementation approach is known)
Outline of the planned changes, including which files will be modified, added, or removed.

## Related PR

(Include when a Proposed Solution is provided)
TBD — will be updated when the PR is created.
```

## Choosing a Tag

Check whether the project defines its own tag list (common locations: `docs/git-msg-tags.md`, `CONTRIBUTING.md`). If none is found, use these defaults:

| Tag | When to use |
|-----|------------|
| `[feat]` | New functionality |
| `[fix]` | Bug fix |
| `[docs]` | Documentation-only change |
| `[refactor]` | Restructuring without behaviour change |
| `[test]` | Test-only change |
| `[chore]` | Tooling, CI, or dependency updates |

For issues that don't fit a standard tag, use descriptive labels like `[bug report]`, `[feature request]`, or `[improvement]`.

When the issue includes a concrete implementation plan in its Proposed Solution, prefix the tag with `[plan]` — for example, `[plan][feat]: Add webhook support`.

## Workflow

### 1. Gather Context

Review the conversation to determine:
- **Issue type**: bug, feature, improvement, or planned work
- **Key details**: what, why, which modules are involved
- If a plan already exists in the conversation, use it as the Proposed Solution

### 2. Select the Tag

- Read the project's tag file if one exists
- Match the issue type to the most fitting tag
- If ambiguous, present 2–3 options and let the user choose

### 3. Draft the Issue

Compose the issue following the structure above:
- **Title**: `[tag]: Summary` — keep the summary under 80 characters
- **Description**: Enough context for someone unfamiliar with the conversation to understand the problem
- **Steps to Reproduce**: Only for bugs; keep it minimal
- **Proposed Solution**: If a plan exists, include it verbatim — do not rewrite it
- **Related PR**: Placeholder if a Proposed Solution is provided

### 4. Confirm with the User

Display the full draft and wait for explicit approval before creating anything.

### 5. Create the Issue

```bash
gh issue create --title "TITLE" --body-file - <<'EOF'
BODY
EOF
```

To update an existing issue instead (when invoked with `--update <number>`):
```bash
gh issue edit <number> --title "TITLE" --body-file - <<'EOF'
BODY
EOF
```

Display the resulting issue URL.

### 6. Handle Errors

| Problem | Action |
|---------|--------|
| GitHub CLI not authenticated | Ask user to run `gh auth login` |
| No context to extract | Ask user for issue details |
| Creation fails | Show the error and suggest checking CLI configuration |

## Ownership

The issue is created on behalf of the user. The AI agent must not add any authorship or co-authorship attribution.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ayazhankadessova) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
