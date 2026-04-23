---
name: issue-fetcher
description: GitLab/GitHub issue 정보를 glab/gh CLI로 가져오기. issue description, comments, 관련 MR/PR (description, comments, branch names), linked issues, parent epic 정보를 JSON 또는 markdown 형식으로 출력. issue 상세 정보가 필요할 때 사용. Use when this capability is needed.
metadata:
  author: guny524
---

# Issue Fetcher (GitLab & GitHub)
GitLab/GitHub issue 정보를 CLI 도구(glab/gh)로 가져오는 방법

## 1. 전제조건
glab CLI (GitLab) 또는 gh CLI (GitHub) 설치 및 인증 필요
- GitLab: `glab auth login`
- GitHub: `gh auth login`

**중요**: GitLab 사용 시 `--platform gitlab --repo OWNER/REPO` 옵션이 **필수**입니다.
- glab이 git remote를 자동 감지하지 못하는 경우가 많음
- `--repo` 옵션을 항상 명시하여 사용해야 함

## 2. 핵심 기능
uv run [fetch_issue.py](~/llms/skills/issue-fetcher/scripts/fetch_issue.py) <issue_id> --platform gitlab --repo OWNER/REPO --format json|markdown 실행

### 2-1. Issue 목록 조회
```bash
scripts/fetch_issue.py list --platform gitlab --repo OWNER/REPO --limit 20
scripts/fetch_issue.py list --platform github --repo OWNER/REPO --search "bug" --label "P0"
```

### 2-2. Issue 상세 조회 (JSON)
```bash
scripts/fetch_issue.py view 779 --platform gitlab --repo mycompany/group/subgroup/myproject
scripts/fetch_issue.py view 121 --platform github --repo owner/repo
```

### 2-3. Issue 상세 조회 (Markdown)
```bash
# GitLab: --platform과 --repo 필수
scripts/fetch_issue.py view 779 --platform gitlab --repo mycompany/group/subgroup/myproject --format markdown
scripts/fetch_issue.py view 20 --platform gitlab --repo mycompany/group/subgroup/another-project --format markdown

# GitHub
scripts/fetch_issue.py view 121 --platform github --repo owner/repo --format markdown

# URL 방식 (GitLab은 --repo도 함께 명시 권장)
scripts/fetch_issue.py view 779 --url https://gitlab.com/mycompany/group/subgroup/myproject/-/issues/779 --repo mycompany/group/subgroup/myproject --format markdown
scripts/fetch_issue.py view 20 --url https://gitlab.com/mycompany/group/subgroup/another-project/-/issues/20 --repo mycompany/group/subgroup/another-project --format markdown
```

## 3. Markdown 출력 형식
JSON 또는 markdown 형식 지원. markdown 형식은 `## 이슈` (이슈번호, 링크, 제목, description, comments, 첨부 이미지), `## 관련된 이슈들` (GitLab만, 제목과 이슈번호 링크만), `## Parent Epic` (GitLab만, epic 번호, 링크, 제목), `## 이슈에 연결된 MR/PR` (MR/PR 번호, 링크, 제목, branch 이름, commit hashes, description, comments, 첨부 이미지) 포함.

## 4. 관련 MR/PR 가져오기

### 4-1. GitLab
REST API `/issues/{id}/related_merge_requests` 사용
- issue와 연결된 모든 MR 정확하게 가져옴 (merged, opened 모두)

### 4-2. GitHub
2가지 전략으로 PR 검색 후 중복 제거
- Strategy 1: PR title/body에 `#{issue_number}` 언급된 것 검색
- Strategy 2: branch name이 `{issue_number}-*` 패턴인 PR 검색 (예: `7-test`, `7-fix`)

## 5. MR/PR 상세 정보

### 5-1. Description과 comments 가져오기
이전 gitlab-issue-fetcher, github-issue-fetcher는 title/state만 가져왔으나 이제 전체 내용 포함

### 5-2. Branch names 표시
`[source-branch → target-branch]` 형식으로 표시
- GitLab: `source_branch`, `target_branch` 필드
- GitHub: `headRefName`, `baseRefName` 필드

### 5-3. Commit hashes 표시
MR/PR에 포함된 모든 commit 목록 표시
- GitLab: REST API `/projects/{id}/merge_requests/{mr_iid}/commits` 사용
- GitHub: `gh pr view --json commits` 사용
- 형식: `` `{short_hash}` {commit_title} (@{author})``

## 6. Linked issues (GitLab만)
REST API `/issues/{id}/links` 사용
- 관련 이슈 메타데이터만 가져오기 (title, iid, link_type, state, URL)
- link_type: "blocks", "is_blocked_by", "relates_to"

## 7. 옵션
- `--no-comments`: Issue comments 제외
- `--no-mrs` / `--no-prs`: 관련 MR/PR 제외
- `--format json`: JSON 출력 (기본값)
- `--format markdown`: Markdown 출력

## 8. References
- [references/gitlab-cli.md](~/llms/skills/issue-fetcher/references/gitlab-cli.md) - glab CLI 명령어 레퍼런스
- [references/github-cli.md](~/llms/skills/issue-fetcher/references/github-cli.md) - gh CLI 명령어 레퍼런스

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/guny524) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
