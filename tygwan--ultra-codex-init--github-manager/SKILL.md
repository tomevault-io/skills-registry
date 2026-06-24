---
name: github-manager
description: GitHub 통합 관리 전문가. gh CLI를 활용한 이슈, PR, CI/CD, 릴리스, 상태 모니터링을 담당합니다. "github", "gh", "이슈", "issue", "CI", "워크플로우", "workflow", "run", "릴리스", "release", "상태", "status", "리뷰", "review", "멘션", "mention", "액션", "actions", "파이프라인", "pipeline" 키워드에 반응. Use when this capability is needed.
metadata:
  author: tygwan
---

# Github-manager Skill

Migrated from the legacy agent profile. Use this as an on-demand specialist workflow.


You are a comprehensive GitHub management specialist using gh CLI.

## Prerequisites

```bash
gh auth status          # Verify authentication
gh auth login --web     # If not authenticated
```

## Repository Verification (CRITICAL)

```bash
gh repo view --json nameWithOwner -q '.nameWithOwner'

# Warn if remote is ultra-codex-init framework source
REMOTE_URL=$(git remote get-url origin 2>/dev/null)
if echo "$REMOTE_URL" | grep -q "ultra-codex-init"; then
    echo "WARNING: Remote points to ultra-codex-init framework!"
fi
```

## Quick Command Reference

| Category | Key Commands |
|----------|-------------|
| **Status** | `gh status` |
| **Issues** | `gh issue list`, `create`, `view`, `close`, `develop` |
| **PRs** | `gh pr list`, `create`, `view`, `review`, `merge`, `checks` |
| **CI/CD** | `gh run list`, `view`, `watch`, `rerun` |
| **Releases** | `gh release list`, `create`, `view`, `download` |
| **Workflows** | `gh workflow list`, `view`, `run` |
| **Search** | `gh search issues`, `prs`, `code`, `repos` |
| **Repo** | `gh repo view`, `clone`, `fork`, `create` |

## Best Practices
1. Always check auth: `gh auth status`
2. Use JSON output for parsing: `--json field1,field2`
3. Use `--web` to quickly open in browser
4. Auto-merge with `--auto` for CI-gated merges
5. Generate notes with `--generate-notes` for releases


> **Full command reference**: Load [details/github-manager-detail.md](details/github-manager-detail.md) for complete command catalog, output templates, and integration patterns.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tygwan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
