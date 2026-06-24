---
name: git-workflow
description: > Use when this capability is needed.
metadata:
  author: taipm
---

# Git Workflow: Gitea & GitHub Integration

Deep platform integration for dev-team agents. All agents use this skill for git operations.

## Platform Detection

```bash
git remote get-url origin
```

| URL contains | Platform | Tools |
|-------------|----------|-------|
| `git.microai.club` | Gitea | `/gitea` skill + Gitea API |
| `github.com` | GitHub | `gh` CLI + GitHub MCP |
| Other/none | Fallback | Local git + markdown docs |

See [references/gitea.md](references/gitea.md) and [references/github.md](references/github.md) for platform-specific commands.

## Branch Strategy

```
main
├── feat/{issue-id}-{description}      # Feature branches
├── fix/{issue-id}-{description}       # Bug fix branches
├── refactor/{issue-id}-{description}  # Refactoring branches
└── release/v{x.y.z}                   # Release branches
```

### Branch Lifecycle
1. Create from `main`: `git checkout -b feat/{issue-id}-{desc} main`
2. Implement (developer agent)
3. Push: `git push -u origin feat/{issue-id}-{desc}`
4. Create PR (devops agent)
5. Merge after approval (manual or auto)

## Issue Management

### Issue Lifecycle
```
OPEN → IN_PROGRESS → IN_REVIEW → TESTING → DONE
```

### Labels (auto-create if not exist)
| Label | Color | Meaning |
|-------|-------|---------|
| `type:feature` | `#0075ca` | New feature |
| `type:bugfix` | `#d73a4a` | Bug fix |
| `type:refactor` | `#a2eeef` | Code refactoring |
| `type:docs` | `#0e8a16` | Documentation |
| `type:test` | `#fbca04` | Testing |
| `status:in-progress` | `#ededed` | Being worked on |
| `status:in-review` | `#c5def5` | Code review |
| `status:testing` | `#bfd4f2` | Being tested |
| `lang:rust` | `#dea584` | Rust code |
| `lang:go` | `#00add8` | Go code |
| `lang:python` | `#3572a5` | Python code |

### Agent → Issue Status Updates
| Agent Stage | Update issue to |
|------------|----------------|
| architect starts | `status:in-progress` |
| developer done | `status:in-review` |
| reviewer approves | `status:testing` |
| tester passes | Close issue via PR |

## PR Workflow

### PR Template
```markdown
## Summary
{From architect design doc}

## Changes
| File | Action | Description |
|------|--------|-------------|
| {path} | CREATE/MODIFY | {what changed} |

## Pipeline Results
- Architect: docs/architecture/design-{id}.md
- Review: APPROVED ({critical} critical, {warning} warnings)
- Tests: {passed}/{total} passed

## Linked Issue
Closes #{issue-id}

## Checklist
- [x] Design doc reviewed
- [x] Code review passed (reviewer agent)
- [x] Tests written and passing
- [x] No security issues found
- [x] CI checks passed
```

### PR Review Comments
When reviewer agent finds issues, post them as PR review comments:
- Map findings to specific file lines
- Use `REQUEST_CHANGES` review status for Critical findings
- Use `COMMENT` for Warning and Nit

### Auto-merge Rules
- Only if all CI checks pass
- Only if reviewer agent approved
- Only if all tests pass
- Never auto-merge to main without user confirmation

## Milestone Integration

Link issues to milestones from `docs/plans/roadmap.md`:
- Each roadmap version → one milestone
- Issues created by what-next Step 4 → assigned to corresponding milestone
- Track milestone progress: `{closed}/{total} issues done`

## Release Workflow

When all issues in a milestone are closed:

1. Create release branch: `release/v{x.y.z}`
2. Update version in project files (Cargo.toml / go.mod / pyproject.toml)
3. Generate changelog from merged PRs
4. Create tag: `v{x.y.z}`
5. Create release (Gitea release / GitHub release)
6. Close milestone

## Important

- Never push directly to main
- Never force push
- Always link PRs to issues (`Closes #N`)
- Update issue labels at each pipeline stage
- Post agent activity as issue comments for traceability

---
> Source: [taipm/setup-c2](https://github.com/taipm/setup-c2) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
