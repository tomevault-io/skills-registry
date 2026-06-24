---
name: git-workflow
description: Git Flow 브랜치 전략 및 PR 절차 가이드. 브랜치 생성, 커밋, PR 작성 시 참조. "branch", "commit", "PR", "pull request", "merge", "git flow" 키워드로 트리거. Use when this capability is needed.
metadata:
  author: eco2-team
---

# Git Workflow Guide

## Branch Strategy (Git Flow)

```
┌─────────────────────────────────────────────────────────────────┐
│                        Branch Flow                               │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  main ────────────────────────────────────────────────────────  │
│    ↑                                                             │
│    │ (release merge)                                            │
│    │                                                             │
│  develop ─────────────────────────────────────────────────────  │
│    ↑         ↑         ↑                                        │
│    │         │         │ (PR merge)                             │
│    │         │         │                                        │
│  feature/  refactor/  fix/                                      │
│  xxx       xxx        xxx                                       │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

## Branch Naming Convention

| Type | Pattern | Example |
|------|---------|---------|
| **Feature** | `feature/{scope}-{description}` | `feature/chat-crud-api` |
| **Refactor** | `refactor/{scope}-{description}` | `refactor/reward-fanout-exchange` |
| **Fix** | `fix/{scope}-{description}` | `fix/auth-token-refresh` |
| **Hotfix** | `hotfix/{description}` | `hotfix/critical-security-patch` |
| **Release** | `release/v{version}` | `release/v1.2.0` |

### Branch Rules

1. **Base Branch**: `develop` (NOT `main`)
2. **Feature/Refactor/Fix**: Branch from `develop`, merge back to `develop`
3. **Hotfix**: Branch from `main`, merge to both `main` and `develop`
4. **Release**: Branch from `develop`, merge to `main` after QA

> ⚠️ **CRITICAL: PR은 반드시 `develop` 브랜치를 base로 생성해야 합니다.**
> - `gh pr create --base develop` (NOT `--base main`)
> - main으로 잘못 머지하면 main → develop PR을 추가로 생성해야 합니다.

## Commit Convention (Conventional Commits)

```
<type>(<scope>): <description>

[optional body]

[optional footer]
Co-Authored-By: Claude Opus 4.5 <noreply@anthropic.com>
```

### Types

| Type | Description | Example |
|------|-------------|---------|
| `feat` | New feature | `feat(chat): add message persistence` |
| `fix` | Bug fix | `fix(auth): resolve token refresh race condition` |
| `refactor` | Code refactoring | `refactor(chat_worker): apply Clean Architecture` |
| `docs` | Documentation | `docs: add API specification` |
| `test` | Tests | `test(chat_worker): add unit tests for intent classifier` |
| `chore` | Maintenance | `chore: update dependencies` |
| `style` | Formatting | `style: fix linting errors` |
| `perf` | Performance | `perf(rag): optimize vector search` |

### Scope Examples

- `auth`, `users`, `chat`, `chat_worker`, `location`, `character`
- `infra`, `k8s`, `terraform`, `helm`
- `ci`, `cd`, `pipeline`

## PR Procedure

### 1. Create Branch

```bash
# From develop branch
git checkout develop
git pull origin develop
git checkout -b feature/chat-crud-api
```

### 2. Work & Commit

```bash
# Make changes
git add .
git commit -m "feat(chat): add message persistence"
```

### 3. Push & Create PR

```bash
# Push to remote
git push -u origin feature/chat-crud-api

# Create PR via gh CLI
gh pr create --base develop --title "feat(chat): add CRUD API for messages" --body "$(cat <<'EOF'
## Summary
- Add message persistence endpoints
- Implement ChatRepository with PostgreSQL

## Test plan
- [ ] Unit tests pass
- [ ] Integration tests with DB
- [ ] Manual API testing

🤖 Generated with [Claude Code](https://claude.com/claude-code)
EOF
)"
```

### 4. PR Review & Merge

1. Wait for CI checks (GitHub Actions)
2. Request review from team members
3. Address review comments
4. Squash and merge to `develop`

## PR Template

```markdown
## Summary
<1-3 bullet points describing changes>

## Related Issue
Closes #123

## Test plan
- [ ] Unit tests pass (`pytest apps/{service}/tests/unit -v`)
- [ ] Integration tests pass
- [ ] Manual testing completed

## Checklist
- [ ] Code follows project conventions
- [ ] Tests added/updated
- [ ] Documentation updated (if needed)
- [ ] No breaking changes (or documented)

🤖 Generated with [Claude Code](https://claude.com/claude-code)
```

## CI/CD Integration

### GitOps 플로우

> **ArgoCD가 `develop` 브랜치를 바라봄** (Staging 아님, Dev 환경 직접 배포)

```
PR → develop 머지 → ArgoCD auto-sync → Dev 클러스터 반영
```

### Branches Triggering CI

| Branch | CI Trigger | Deploy Target |
|--------|------------|---------------|
| `main` | Push | (예비, 현재 미사용) |
| `develop` | Push, PR | **Dev (ArgoCD sync)** |
| `feature/*` | PR only | - |
| `refactor/*` | PR only | - |
| `fix/*` | PR only | - |

### Quality Gates

- Linting (ruff, black)
- Type checking (mypy)
- Unit tests (pytest)
- Security scan (SonarCloud)

## Quick Reference

### Common Commands

```bash
# Start new feature
git checkout develop && git pull && git checkout -b feature/new-feature

# Sync with develop
git fetch origin develop && git rebase origin/develop

# Create PR
gh pr create --base develop

# Check PR status
gh pr status

# View PR in browser
gh pr view --web
```

### Useful gh CLI Commands

```bash
# List open PRs
gh pr list

# Check out PR locally
gh pr checkout 123

# Approve PR
gh pr review --approve

# Merge PR
gh pr merge --squash --delete-branch
```

## Reference Files

- **Branch naming**: See [branch-naming.md](./references/branch-naming.md)
- **Commit examples**: See [commit-examples.md](./references/commit-examples.md)
- **Token streaming fix**: See [token-streaming-fix.md](./references/token-streaming-fix.md) - Worktree + 트러블슈팅 사례
- **PR templates**: See `.github/PULL_REQUESTS/`
- **Issue templates**: See `.github/ISSUE_TEMPLATE/`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eco2-team) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
