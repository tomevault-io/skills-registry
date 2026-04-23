---
name: github-issue
description: GitHub issue lifecycle management with worktree isolation Use when this capability is needed.
metadata:
  author: randlee
---

# GitHub Issue Lifecycle Skill

Manage GitHub issues from listing to fixing to PR creation with clean worktree isolation.

## Commands

- `/github-issue` - Main entry point for all issue operations

## Agents

- `issue-intake-agent` - List and fetch issue details
- `issue-mutate-agent` - Create and update issues
- `issue-fix-agent` - Implement fixes in isolated worktrees
- `issue-pr-agent` - Create pull requests

## Operations

- `--list [--repo owner/repo]` - List open issues
- `--create` - Create issue interactively
- `--update <id>` - Update issue fields
- `--fix --issue <id/url> [--yolo]` - Full fix workflow: fetch → confirm → worktree → implement → test → commit → push → PR

## Dependencies

- **Skills**: sc-managing-worktrees (worktree operations)
- **CLI**: GitHub CLI (`gh`) required
- **Config**: `.claude/config.yaml` (base_branch, worktree_root, github settings)

## Configuration

```yaml
base_branch: main
worktree_root: ../worktrees
github:
  branch_pattern: "fix-issue-{number}"
```

## Data Contracts

All agents return fenced JSON:
```json
{
  "success": true|false,
  "data": { /* operation results */ },
  "error": null|"message"
}
```

## Safety

- Pre-flight `gh` CLI auth checks
- Approval gates before destructive ops (unless `--yolo`)
- Test failure prompts
- Actionable error messages

## References

- `.claude/references/github-issue-apis.md` - GitHub CLI patterns
- `.claude/references/github-issue-checklists.md` - Workflow checklists

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/randlee) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
