---
name: git-workflow
description: 브랜칭 전략, 커밋 컨벤션, merge vs rebase, 충돌 해결 및 모든 규모의 팀을 위한 협업 개발 모범 사례를 포함한 Git 워크플로우 패턴입니다. Use when this capability is needed.
metadata:
  author: SAM42-Lab
---

# Git 워크플로우 패턴

Git 버전 관리, 브랜칭 전략 및 협업 개발을 위한 모범 사례입니다.

## 활성화 시점

- 새 프로젝트를 위한 Git 워크플로우 설정 시
- 브랜칭 전략(GitFlow, trunk-based, GitHub flow) 결정 시
- 커밋 메시지 및 PR 설명 작성 시
- 병합 충돌(merge conflicts) 해결 시
- 릴리스 및 버전 태그 관리 시
- 신규 팀원에게 Git 관행 교육 시

## 브랜칭 전략

### GitHub Flow (단순함, 대부분의 팀에 권장)

지속적 배포와 소규모 및 중규모 팀에 가장 적합합니다.

```
main (보호됨, 항상 배포 가능)
  │
  ├── feature/user-auth      → PR → main으로 병합
  ├── feature/payment-flow   → PR → main으로 병합
  └── fix/login-bug          → PR → main으로 병합
```

**규칙:**
- `main` 브랜치는 항상 배포 가능해야 합니다.
- `main`에서 기능(feature) 브랜치를 생성합니다.
- 리뷰 준비가 되면 Pull Request를 엽니다.
- 승인 및 CI 통과 후 `main`으로 병합합니다.
- 병합 후 즉시 배포합니다.

### Trunk-Based Development (고속 개발 팀)

강력한 CI/CD 및 기능 플래그(feature flags)를 사용하는 팀에 적합합니다.

```
main (trunk)
  │
  ├── 단기 기능 브랜치 (최대 1-2일)
  ├── 단기 기능 브랜치
  └── 단기 기능 브랜치
```

**규칙:**
- 모든 사람이 `main` 또는 매우 짧은 수명의 브랜치에 커밋합니다.
- 기능 플래그를 사용하여 미완성 작업을 숨깁니다.
- 병합 전 반드시 CI를 통과해야 합니다.
- 하루에 여러 번 배포합니다.

### GitFlow (복잡함, 릴리스 주기 중심)

예정된 릴리스와 엔터프라이즈 프로젝트에 적합합니다.

```
main (프로덕션 릴리스)
  │
  └── develop (통합 브랜치)
        │
        ├── feature/user-auth
        ├── feature/payment
        │
        ├── release/1.0.0    → main 및 develop으로 병합
        │
        └── hotfix/critical  → main 및 develop으로 병합
```

**규칙:**
- `main`은 프로덕션 준비가 된 코드만 포함합니다.
- `develop`은 통합 브랜치입니다.
- `develop`에서 기능 브랜치를 생성하고 다시 `develop`으로 병합합니다.
- `develop`에서 릴리스 브랜치를 생성하고 `main`과 `develop`으로 병합합니다.
- `main`에서 핫픽스 브랜치를 생성하고 `main`과 `develop` 모두에 병합합니다.

### 전략 선택 가이드

| 전략 | 팀 규모 | 릴리스 주기 | 적합한 분야 |
|----------|-----------|-----------------|----------|
| GitHub Flow | 제한 없음 | 지속적 | SaaS, 웹 앱, 스타트업 |
| Trunk-Based | 숙련된 5인 이상 | 일일 수회 | 고속 개발 팀, 기능 플래그 사용 |
| GitFlow | 10인 이상 | 정기적 | 엔터프라이즈, 규제 산업 |

## 커밋 메시지

### Conventional Commits 형식

```
<type>(<scope>): <subject>

[본문(선택 사항)]

[바닥글(선택 사항)]
```

### 유형(Types)

| 유형 | 용도 | 예시 |
|------|---------|---------|
| `feat` | 새로운 기능 | `feat(auth): add OAuth2 login` |
| `fix` | 버그 수정 | `fix(api): handle null response in user endpoint` |
| `docs` | 문서 수정 | `docs(readme): update installation instructions` |
| `style` | 코드 의미 변화 없는 스타일 수정 | `style: fix indentation in login component` |
| `refactor` | 코드 리팩터링 | `refactor(db): extract connection pool to module` |
| `test` | 테스트 추가/수정 | `test(auth): add unit tests for token validation` |
| `chore` | 빌드 업무, 패키지 매니저 설정 등 | `chore(deps): update dependencies` |
| `perf` | 성능 개선 | `perf(query): add index to users table` |
| `ci` | CI/CD 설정 변경 | `ci: add PostgreSQL service to test workflow` |
| `revert` | 이전 커밋 되돌리기 | `revert: revert "feat(auth): add OAuth2 login"` |

### 좋은 예 vs 나쁜 예

```bash
# 나쁨: 모호하고 문맥이 없음
git commit -m "fixed stuff"
git commit -m "updates"
git commit -m "WIP"

# 좋음: 명확하고 구체적이며 이유를 설명함
git commit -m "fix(api): retry requests on 503 Service Unavailable

외부 API가 피크 시간대에 가끔 503 오류를 반환합니다.
최대 3회 시도의 지수 백오프 재시도 로직을 추가했습니다.

Closes #123"
```

### 커밋 메시지 템플릿

저장소 루트에 `.gitmessage` 생성:

```
# <type>(<scope>): <subject>
# # 유형: feat, fix, docs, style, refactor, test, chore, perf, ci, revert
# 범위: api, ui, db, auth 등
# 제목: 명령형, 마침표 없음, 최대 50자
#
# [본문 선택 사항] - 무엇이 아닌 '왜'를 설명하십시오.
# [바닥글 선택 사항] - 주요 변경 사항(Breaking changes), 이슈 번호(Closes #issue)
```

적용 방법: `git config commit.template .gitmessage`

## 병합(Merge) vs 리베이스(Rebase)

### Merge (기록 보존)

```bash
# 병합 커밋 생성
git checkout main
git merge feature/user-auth

# 결과:
# *   merge commit
# |\
# | * feature commits
# |/
# * main commits
```

**사용 시점:**
- 기능 브랜치를 `main`으로 병합할 때
- 정확한 역사를 보존하고 싶을 때
- 여러 사람이 해당 브랜치에서 작업했을 때
- 브랜치가 이미 푸시되어 다른 사람이 이를 기반으로 작업 중일 때

### Rebase (선형적 역사)

```bash
# 대상 브랜치 위로 기능 커밋들을 다시 씀
git checkout feature/user-auth
git rebase main

# 결과:
# * feature commits (rewritten)
# * main commits
```

**사용 시점:**
- 로컬 기능 브랜치를 최신 `main` 상태로 업데이트할 때
- 깨끗하고 선형적인 역사를 원할 때
- 브랜치가 로컬 전용일 때 (푸시되지 않음)
- 나 혼자만 작업하는 브랜치일 때

### Rebase 워크플로우

```bash
# PR 전 최신 main으로 기능 브랜치 업데이트
git checkout feature/user-auth
git fetch origin
git rebase origin/main

# 충돌 해결
# 테스트 통과 확인

# 강제 푸시 (본인만 기여자인 경우에만)
git push --force-with-lease origin feature/user-auth
```

### Rebase를 하면 안 되는 경우

```
# 다음 브랜치는 절대 rebase하지 마십시오:
- 공유 저장소에 푸시된 브랜치
- 다른 사람이 이를 기반으로 작업을 시작한 브랜치
- 보호된 브랜치 (main, develop)
- 이미 병합된 브랜치

# 이유: Rebase는 역사를 다시 쓰므로 다른 사람의 작업을 망칠 수 있습니다.
```

## Pull Request 워크플로우

### PR 제목 형식

```
<type>(<scope>): <description>

예시:
feat(auth): add SSO support for enterprise users
fix(api): resolve race condition in order processing
docs(api): add OpenAPI specification for v2 endpoints
```

### PR 설명 템플릿

```markdown
## 내용 (What)

이 PR이 수행하는 작업에 대한 짧은 설명입니다.

## 이유 (Why)

동기와 문맥을 설명하십시오.

## 방법 (How)

강조할 만한 주요 구현 세부 사항입니다.

## 테스트 (Testing)

- [ ] 단위 테스트 추가/업데이트
- [ ] 통합 테스트 추가/업데이트
- [ ] 수동 테스트 수행

## 스크린샷 (해당하는 경우)

UI 변경 사항에 대한 전/후 스크린샷입니다.

## 체크리스트

- [ ] 코드가 프로젝트 스타일 가이드를 따릅니다.
- [ ] 셀프 리뷰를 완료했습니다.
- [ ] 복잡한 로직에 주석을 추가했습니다.
- [ ] 문서를 업데이트했습니다.
- [ ] 새로운 경고가 도입되지 않았습니다.
- [ ] 로컬에서 테스트가 통과합니다.
- [ ] 관련 이슈를 연결했습니다.

Closes #123
```

### 코드 리뷰 체크리스트

**리뷰어용:**

- [ ] 코드가 명시된 문제를 해결합니까?
- [ ] 처리되지 않은 예외 사례가 있습니까?
- [ ] 코드가 가독성 있고 유지보수 가능합니까?
- [ ] 테스트가 충분합니까?
- [ ] 보안상 우려 사항이 있습니까?
- [ ] 커밋 기록이 깨끗합니까 (필요시 squash)?

**작성자용:**

- [ ] 리뷰 요청 전 셀프 리뷰를 완료했습니까?
- [ ] CI(테스트, 린트, 타입 체크)가 통과합니까?
- [ ] PR 크기가 적절합니까 (500라인 미만 권장)?
- [ ] 단일 기능/수정에 집중되어 있습니까?
- [ ] 설명이 변경 사항을 명확하게 설명합니까?

## 충돌(Conflict) 해결

### 충돌 식별

```bash
# 병합 전 충돌 확인
git checkout main
git merge feature/user-auth --no-commit --no-ff

# 충돌 발생 시 Git의 메시지:
# CONFLICT (content): Merge conflict in src/auth/login.ts
# Automatic merge failed; fix conflicts and then commit the result.
```

### 충돌 해결

```bash
# 충돌된 파일 확인
git status

# 파일 내 충돌 마커 확인
# <<<<<<< HEAD
# main 브랜치의 내용
# =======
# 기능 브랜치의 내용
# >>>>>>> feature/user-auth

# 옵션 1: 수동 해결
# 파일을 편집하여 마커를 제거하고 올바른 내용만 남깁니다.

# 옵션 2: 병합 도구 사용
git mergetool

# 옵션 3: 한쪽 편 내용 수용
git checkout --ours src/auth/login.ts    # main 버전 유지
git checkout --theirs src/auth/login.ts  # 기능 브랜치 버전 유지

# 해결 후 스테이징 및 커밋
git add src/auth/login.ts
git commit
```

### 충돌 방지 전략

```bash
# 1. 기능 브랜치를 작고 짧게 유지하십시오.
# 2. 주기적으로 main을 rebase 하십시오.
git checkout feature/user-auth
git fetch origin
git rebase origin/main

# 3. 공유 파일을 수정할 때는 팀과 소통하십시오.
# 4. 수명이 긴 브랜치 대신 기능 플래그를 사용하십시오.
# 5. PR을 신속하게 검토하고 병합하십시오.
```

## 브랜치 관리

### 명명 규칙 (Naming Conventions)

```
# 기능 브랜치
feature/user-authentication
feature/JIRA-123-payment-integration

# 버그 수정
fix/login-redirect-loop
fix/456-null-pointer-exception

# 핫픽스 (프로덕션 이슈)
hotfix/critical-security-patch
hotfix/database-connection-leak

# 릴리스
release/1.2.0
release/2024-01-hotfix

# 실험/POC
experiment/new-caching-strategy
poc/graphql-migration
```

### 브랜치 정리

```bash
# 병합된 로컬 브랜치 삭제
git branch --merged main | grep -v "^\*\|main" | xargs -n 1 git branch -d

# 삭제된 원격 브랜치에 대한 원격 추적 참조 삭제
git fetch -p

# 로컬 브랜치 삭제
git branch -d feature/user-auth  # 안전한 삭제 (병합된 경우에만)
git branch -D feature/user-auth  # 강제 삭제

# 원격 브랜치 삭제
git push origin --delete feature/user-auth
```

### Stash 워크플로우

```bash
# 작업 중인 내용 저장
git stash push -m "WIP: user authentication"

# Stash 목록 확인
git stash list

# 최근 Stash 적용 및 제거
git stash pop

# 특정 Stash 적용
git stash apply stash@{2}

# Stash 삭제
git stash drop stash@{0}
```

## 릴리스 관리

### 시맨틱 버저닝 (Semantic Versioning)

```
MAJOR.MINOR.PATCH

MAJOR: 하위 호환되지 않는 변경
MINOR: 하위 호환되는 기능 추가
PATCH: 하위 호환되는 버그 수정

예시:
1.0.0 → 1.0.1 (patch: 버그 수정)
1.0.1 → 1.1.0 (minor: 새 기능 추가)
1.1.0 → 2.0.0 (major: 중대한 변경)
```

### 릴리스 생성

```bash
# 주석이 달린 태그 생성
git tag -a v1.2.0 -m "Release v1.2.0

Features:
- 사용자 인증 추가
- 비밀번호 재설정 구현

Fixes:
- 로그인 리다이렉트 문제 해결

Breaking Changes:
- 없음"

# 태그 원격 푸시
git push origin v1.2.0

# 태그 목록 확인
git tag -l

# 태그 삭제
git tag -d v1.2.0
git push origin --delete v1.2.0
```

### 변경 로그(Changelog) 생성

```bash
# 커밋에서 변경 로그 생성
git log v1.1.0..v1.2.0 --oneline --no-merges

# 또는 conventional-changelog 사용
npx conventional-changelog -i CHANGELOG.md -s
```

## Git 설정 (Configuration)

### 필수 설정

```bash
# 사용자 정보
git config --global user.name "Your Name"
git config --global user.email "your@email.com"

# 기본 브랜치 이름
git config --global init.defaultBranch main

# Pull 동작 (병합 대신 리베이스)
git config --global pull.rebase true

# Push 동작 (현재 브랜치만 푸시)
git config --global push.default current

# 오타 자동 수정
git config --global help.autocorrect 1

# 더 나은 diff 알고리즘
git config --global diff.algorithm histogram

# 컬러 출력
git config --global color.ui auto
```

### 유용한 별칭 (Aliases)

```bash
# ~/.gitconfig 에 추가
[alias]
    co = checkout
    br = branch
    ci = commit
    st = status
    unstage = reset HEAD --
    last = log -1 HEAD
    visual = log --oneline --graph --all
    amend = commit --amend --no-edit
    wip = commit -m "WIP"
    undo = reset --soft HEAD~1
    contributors = shortlog -sn
```

### .gitignore 패턴

```gitignore
# 의존성
node_modules/
vendor/

# 빌드 결과물
dist/
build/
*.o
*.exe

# 환경 변수 파일
.env
.env.local
.env.*.local

# IDE
.idea/
.vscode/
*.swp
*.swo

# OS 파일
.DS_Store
Thumbs.db

# 로그
*.log
logs/

# 테스트 커버리지
coverage/

# 캐시
.cache/
*.tsbuildinfo
```

## 일반적인 워크플로우

### 새로운 기능 시작하기

```bash
# 1. main 브랜치 업데이트
git checkout main
git pull origin main

# 2. 기능 브랜치 생성
git checkout -b feature/user-auth

# 3. 변경 사항 작성 및 커밋
git add .
git commit -m "feat(auth): implement OAuth2 login"

# 4. 원격에 푸시
git push -u origin feature/user-auth

# 5. GitHub/GitLab에서 Pull Request 생성
```

### PR에 새로운 변경 사항 업데이트하기

```bash
# 1. 추가 변경 사항 작성
git add .
git commit -m "feat(auth): add error handling"

# 2. 업데이트 푸시
git push origin feature/user-auth
```

### Fork한 저장소 동기화하기

```bash
# 1. 원본(upstream) 원격 추가 (한 번만 수행)
git remote add upstream https://github.com/original/repo.git

# 2. 원본 패치
git fetch upstream

# 3. 원본 main을 본인의 main으로 병합
git checkout main
git merge upstream/main

# 4. 본인의 포크(origin)로 푸시
git push origin main
```

### 실수 되돌리기

```bash
# 마지막 커밋 취소 (변경 사항 유지)
git reset --soft HEAD~1

# 마지막 커밋 취소 (변경 사항 삭제)
git reset --hard HEAD~1

# 이미 푸시된 마지막 커밋 되돌리기
git revert HEAD
git push origin main

# 특정 파일 변경 사항 취소
git checkout HEAD -- path/to/file

# 마지막 커밋 메시지 수정
git commit --amend -m "새로운 메시지"

# 누락된 파일을 마지막 커밋에 추가
git add forgotten-file
git commit --amend --no-edit
```

## Git Hooks

### Pre-Commit Hook

```bash
#!/bin/bash
# .git/hooks/pre-commit

# 린트 실행
npm run lint || exit 1

# 테스트 실행
npm test || exit 1

# 비밀 키 포함 여부 확인
if git diff --cached | grep -E '(password|api_key|secret)'; then
    echo "비밀 정보가 감지되었습니다. 커밋이 중단되었습니다."
    exit 1
fi
```

### Pre-Push Hook

```bash
#!/bin/bash
# .git/hooks/pre-push

# 전체 테스트 슈트 실행
npm run test:all || exit 1

# console.log 구문 확인
if git diff origin/main | grep -E 'console\.log'; then
    echo "푸시 전 console.log 구문을 제거하십시오."
    exit 1
fi
```

## 안티 패턴 (Anti-Patterns)

```bash
# 나쁨: main 브랜치에 직접 커밋
git checkout main
git commit -m "fix bug"

# 좋음: 기능 브랜치와 PR을 사용하십시오.

# 나쁨: 비밀 키 커밋
git add .env  # API 키 포함됨

# 좋음: .gitignore에 추가하고 환경 변수를 사용하십시오.

# 나쁨: 거대한 PR (1000라인 이상)
# 좋음: 작고 집중된 PR로 나누십시오.

# 나쁨: "Update" 같은 무의미한 커밋 메시지
git commit -m "update"
git commit -m "fix"

# 좋음: 설명적인 메시지
git commit -m "fix(auth): resolve redirect loop after login"

# 나쁨: 공개된 역사를 강제 푸시로 다시 쓰기
git push --force origin main

# 좋음: 공개 브랜치에서는 revert를 사용하십시오.
git revert HEAD

# 나쁨: 수명이 긴 기능 브랜치 (몇 주/몇 달)
# 좋음: 브랜치 수명을 짧게(며칠) 유지하고 자주 rebase 하십시오.

# 나쁨: 생성된 파일 커밋
git add dist/
git add node_modules/

# 좋음: .gitignore에 추가하십시오.
```

## 빠른 참조 (Quick Reference)

| 작업 | 명령어 |
|------|---------|
| 브랜치 생성 | `git checkout -b feature/name` |
| 브랜치 전환 | `git checkout branch-name` |
| 브랜치 삭제 | `git branch -d branch-name` |
| 브랜치 병합 | `git merge branch-name` |
| 브랜치 리베이스 | `git rebase main` |
| 기록 보기 | `git log --oneline --graph` |
| 변경 사항 확인 | `git diff` |
| 변경 사항 스테이징 | `git add .` 또는 `git add -p` |
| 커밋 | `git commit -m "메시지"` |
| 푸시 | `git push origin branch-name` |
| 풀 | `git pull origin branch-name` |
| 임시 저장 | `git stash push -m "메시지"` |
| 마지막 커밋 취소 | `git reset --soft HEAD~1` |
| 커밋 되돌리기 | `git revert HEAD` |

---
> Source: [SAM42-Lab/everything-claude-code-kr](https://github.com/SAM42-Lab/everything-claude-code-kr) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
