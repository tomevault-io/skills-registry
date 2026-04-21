---
name: commit-push-pr
description: Create commits, push to remote, and open a PR/MR - auto-detects GitHub/Gitea/GitLab and skips PR if on the default branch Use when this capability is needed.
metadata:
  author: jason-hchsieh
---

# Commit, Push, and Create PR

You are creating commits, pushing to remote, and opening a pull request or merge request. This skill composes `/commit-push` with platform-aware PR creation.

## Workflow

### 1. Detect Platform and Branch

Run the platform detection script:

```bash
bash "$CLAUDE_PLUGIN_ROOT/scripts/detect-platform.sh"
```

Parse the output. Key fields for PR creation:
- `platform` — determines which CLI to use
- `cli_tool` / `cli_available` — required for PR creation
- `current_branch` / `default_branch` / `is_default_branch`

**If on default branch** (`is_default_branch=true`): Skip PR creation. Warn the user:
```
You're on the default branch (main). Commits will be pushed directly.
No PR will be created. Switch to a feature branch first if you want a PR.
```

**If CLI tool not available**: Commits and push will work, but PR creation requires the platform CLI. Show install instructions and offer to just commit+push without PR.

### 2. Commit and Push

Follow the `/commit-push` skill workflow (pass through `--review` flag if provided):
1. Detect platform
2. Create commits — if `--review`: show plan and wait for approval. Otherwise: execute directly.
3. Push to remote

### 3. Create Pull Request / Merge Request

Only if NOT on the default branch and CLI tool is available.

**Analyze all commits** on this branch vs the default branch to draft the PR:

```bash
git log <default-branch>..HEAD --oneline
```

Also check the full diff for context:
```bash
git diff <default-branch>...HEAD --stat
```

**Draft the PR:**
- Title: Short summary (under 70 characters), inferred from commits
- Body: Summary of changes with bullet points

**Create the PR using the platform CLI:**

**GitHub:**
```bash
gh pr create --title "<title>" --body "$(cat <<'EOF'
## Summary
<bullet points>

## Test plan
<checklist>
EOF
)"
```

**Gitea:**
```bash
tea pr create --title "<title>" --description "$(cat <<'EOF'
## Summary
<bullet points>
EOF
)"
```

**GitLab:**
```bash
glab mr create --title "<title>" --description "$(cat <<'EOF'
## Summary
<bullet points>
EOF
)"
```

**Fallback (no CLI):** Show the user a formatted PR description they can copy, and provide the URL to create it manually.

### 4. Report

```
## Commit, Push & PR Summary

Platform: GitHub (gh)

### Commits
1. abc1234 feat: add JWT refresh (3 files)
2. def5678 docs: document JWT refresh (1 file)

### Push
Branch: feature/jwt-refresh → origin/feature/jwt-refresh

### Pull Request
Title: Add JWT token refresh on expiry
URL: https://github.com/owner/repo/pull/42
Status: Open (draft: no)
```

## Configuration

Check `.claude/git.local.md` for PR preferences:

```yaml
---
pr_draft: false       # Create PRs as draft by default
pr_labels: []         # Default labels to add
pr_reviewers: []      # Default reviewers to request
pr_base: ""           # Override base branch (default: auto-detect)
---
```

Apply these when creating the PR:
- `pr_draft: true` → add `--draft` flag
- `pr_labels` → add `--label` flags (GitHub/GitLab)
- `pr_reviewers` → add `--reviewer` flags (GitHub)
- `pr_base` → add `--base` flag to override target branch

## Arguments

| Argument | Effect |
|----------|--------|
| (none) | Auto-detect everything, commit directly |
| `--review` | Show commit plan and wait for approval before executing |
| `--draft` | Create PR as draft |
| `--style conventional` | Force commit style |
| `--no-split` | Single commit |
| `--no-pr` | Skip PR creation (same as /commit-push) |

## Edge Cases

- **On default branch**: Skip PR, warn user, just commit+push
- **PR already exists**: Detect with `gh pr view` / `tea pr list` / `glab mr list`, report existing PR URL instead of creating duplicate
- **No CLI tool**: Commit+push succeeds, show manual PR creation instructions
- **No changes**: Report "Nothing to commit"
- **Branch not pushed yet**: The push step handles `-u` flag for new branches

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jason-hchsieh) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
