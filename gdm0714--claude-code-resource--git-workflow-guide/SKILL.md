---
name: git-workflow-guide
description: Git 워크플로우 가이드 - 혼자서도, 팀에서도 깔끔한 커밋 히스토리 유지하기 Use when this capability is needed.
metadata:
  author: gdm0714
---

# Git 워크플로우 가이드

## 핵심 원칙

> "커밋 히스토리는 프로젝트의 스토리다. 읽기 쉽게 작성하라."

### 1. 작은 커밋, 자주 커밋
- 한 번에 하나의 변경사항만
- 커밋은 저렴하다, 두려워하지 마라

### 2. 의미 있는 커밋 메시지
- 무엇을 했는지보다 왜 했는지
- 6개월 후의 나를 위해

### 3. main/master는 항상 배포 가능한 상태
- CI/CD 통과한 것만 merge
- 직접 push 금지

---

## 커밋 메시지 작성법

### 기본 구조

```bash
# 구조
타입(범위): 제목

본문 (선택)

푸터 (선택)

# 예시
feat(auth): 카카오 로그인 추가

사용자가 카카오 계정으로 간편하게 로그인할 수 있도록
NextAuth.js를 사용한 카카오 OAuth 연동을 구현했습니다.

Closes #123
```

### 커밋 타입

```bash
# 주요 타입
feat:     새 기능
fix:      버그 수정
docs:     문서 변경
style:    코드 포맷팅 (기능 변경 없음)
refactor: 리팩토링
test:     테스트 추가/수정
chore:    빌드, 설정 변경

# 예시
feat: 결제 기능 추가
fix: 로그인 에러 수정
docs: README 업데이트
style: ESLint 규칙 적용
refactor: API 호출 로직 개선
test: 사용자 등록 테스트 추가
chore: 패키지 업데이트
```

### ✅ 좋은 커밋 메시지

```bash
# ✅ 명확하고 구체적
feat(payment): 토스페이먼츠 결제 연동 추가

# ✅ 이슈 번호 포함
fix: 로그인 실패 시 에러 메시지 표시 (#42)

# ✅ 왜 했는지 설명
refactor: API 호출을 React Query로 변경

기존 useEffect + useState 조합은 로딩 상태 관리가 복잡했고,
캐싱이 없어 불필요한 API 호출이 많았습니다.
React Query로 변경하여 자동 캐싱과 재시도 기능을 활용합니다.
```

### ❌ 나쁜 커밋 메시지

```bash
# ❌ 너무 모호
update code
fix bug
changes

# ❌ 의미 없는 메시지
asdf
test
aaa
수정

# ❌ 커밋이 너무 큼
feat: 전체 기능 구현 (로그인, 회원가입, 프로필, 설정, ...)
```

---

## 브랜치 전략

### 브랜치 네이밍

```bash
# 기능 개발
feature/login-ui
feature/payment-integration

# 버그 수정
fix/login-error
hotfix/critical-bug

# 리팩토링
refactor/api-client

# 문서
docs/readme-update

# 예시
git checkout -b feature/kakao-login
git checkout -b fix/payment-validation
```

### Git Flow (팀 프로젝트)

```bash
# 브랜치 구조
main/master     → 프로덕션 (배포용)
develop         → 개발 통합
feature/*       → 기능 개발
hotfix/*        → 긴급 수정

# 워크플로우
1. develop에서 feature 브랜치 생성
git checkout develop
git checkout -b feature/user-profile

2. 작업 후 커밋
git add .
git commit -m "feat: 사용자 프로필 페이지 추가"

3. develop에 merge
git checkout develop
git merge feature/user-profile

4. 배포 준비되면 main에 merge
git checkout main
git merge develop
git tag v1.2.0
```

### GitHub Flow (심플)

```bash
# 브랜치 구조
main            → 항상 배포 가능
feature/*       → 모든 작업

# 워크플로우
1. main에서 브랜치 생성
git checkout main
git checkout -b feature/new-feature

2. 작업 & 푸시
git push origin feature/new-feature

3. Pull Request 생성
4. 리뷰 후 main에 merge
5. 자동 배포 (CI/CD)
```

---

## 일상 작업

### 시작: 최신 코드 받기

```bash
# 1. 메인 브랜치 최신화
git checkout main
git pull origin main

# 2. 새 브랜치 생성
git checkout -b feature/new-feature

# 또는 한 줄로
git checkout -b feature/new-feature origin/main
```

### 작업 중: 커밋

```bash
# 변경사항 확인
git status
git diff

# 스테이징
git add src/components/Login.tsx
# 또는 모든 변경사항
git add .

# 커밋
git commit -m "feat(auth): 로그인 UI 추가"

# 푸시
git push origin feature/new-feature
```

### 완료: Pull Request

```bash
# 1. 푸시
git push origin feature/login

# 2. GitHub에서 PR 생성
# 3. 리뷰 대기
# 4. 승인 후 merge
```

---

## 충돌 해결

### 충돌 발생 시

```bash
# 1. 메인 브랜치 최신 코드 가져오기
git checkout main
git pull origin main

# 2. 작업 브랜치로 돌아가서 merge
git checkout feature/my-feature
git merge main

# 3. 충돌 파일 확인
git status

# 4. 충돌 해결
# 파일 열어서 수정
# <<<<<<< HEAD
# 내 코드
# =======
# 메인 브랜치 코드
# >>>>>>> main

# 5. 해결 후 커밋
git add .
git commit -m "Merge main into feature/my-feature"

# 6. 푸시
git push origin feature/my-feature
```

---

## 실수 복구

### 커밋 취소

```bash
# 마지막 커밋 취소 (변경사항 유지)
git reset --soft HEAD~1

# 마지막 커밋 취소 (변경사항 삭제)
git reset --hard HEAD~1

# 특정 커밋으로 되돌리기
git reset --hard abc123
```

### 커밋 메시지 수정

```bash
# 마지막 커밋 메시지 수정
git commit --amend -m "새로운 메시지"

# 마지막 커밋에 파일 추가
git add forgotten-file.txt
git commit --amend --no-edit
```

### 푸시 취소

```bash
# ⚠️ 주의: 이미 다른 사람이 pull 했다면 사용 금지!

# 로컬 커밋 취소
git reset --hard HEAD~1

# 강제 푸시
git push origin feature/my-feature --force

# ✅ 안전한 방법: revert 사용
git revert HEAD
git push origin feature/my-feature
```

---

## 유용한 명령어

### 히스토리 보기

```bash
# 간단한 로그
git log --oneline

# 그래프로 보기
git log --oneline --graph --all

# 특정 파일 히스토리
git log -- src/components/Login.tsx

# 특정 기간
git log --since="2 weeks ago" --until="yesterday"
```

### 변경사항 확인

```bash
# 스테이징되지 않은 변경사항
git diff

# 스테이징된 변경사항
git diff --staged

# 특정 파일만
git diff src/utils/api.ts

# 브랜치 간 차이
git diff main..feature/my-feature
```

### Stash (임시 저장)

```bash
# 변경사항 임시 저장
git stash

# 다른 작업...

# 복원
git stash pop

# Stash 목록
git stash list

# 특정 stash 복원
git stash apply stash@{0}
```

---

## 협업 규칙

### Pull Request 작성

```markdown
## 변경사항
- 카카오 로그인 기능 추가
- 로그인 UI 개선
- 에러 처리 추가

## 스크린샷
![Login Screen](./screenshot.png)

## 테스트
- [ ] 로그인 성공
- [ ] 로그인 실패 (잘못된 정보)
- [ ] 네트워크 에러 처리

## 관련 이슈
Closes #123
```

### 코드 리뷰 요청

```bash
# 1. PR 생성
# 2. 리뷰어 지정
# 3. 라벨 추가 (feature, bug, urgent 등)
# 4. 설명 작성

# 리뷰 반영 후
git add .
git commit -m "refactor: 리뷰 반영"
git push origin feature/my-feature
```

---

## .gitignore 설정

### Node.js 프로젝트

```bash
# .gitignore

# 의존성
node_modules/
.pnp
.pnp.js

# 빌드
.next/
out/
dist/
build/

# 환경 변수
.env
.env.local
.env.*.local

# 로그
npm-debug.log*
yarn-debug.log*
yarn-error.log*

# IDE
.vscode/
.idea/
*.swp
*.swo
*~

# OS
.DS_Store
Thumbs.db

# 테스트
coverage/

# 기타
.cache/
```

---

## CI/CD 연동

### GitHub Actions

```yaml
# .github/workflows/ci.yml
name: CI

on:
  pull_request:
    branches: [main, develop]
  push:
    branches: [main, develop]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'

      - name: Install dependencies
        run: npm ci

      - name: Run linter
        run: npm run lint

      - name: Run tests
        run: npm test

      - name: Build
        run: npm run build
```

---

## 트러블슈팅

### 문제 1: 너무 큰 파일 커밋

```bash
# 파일이 100MB 넘으면 GitHub에서 거부

# 해결: Git LFS 사용
git lfs install
git lfs track "*.psd"
git add .gitattributes
git commit -m "chore: Git LFS 설정"
```

### 문제 2: 민감 정보 커밋

```bash
# 😱 .env 파일을 실수로 커밋

# 1. 즉시 파일 삭제 (히스토리에는 남음)
git rm .env
git commit -m "fix: .env 파일 제거"

# 2. 히스토리에서 완전 제거
# git-filter-repo 설치 필요
git filter-repo --path .env --invert-paths

# 3. 강제 푸시
git push origin --force --all

# 4. .env의 키 즉시 변경!
# GitHub에 이미 노출되었으므로 키를 재발급하세요
```

### 문제 3: 브랜치가 너무 뒤쳐짐

```bash
# main 브랜치가 많이 앞서감

# 방법 1: Merge (히스토리 유지)
git checkout feature/my-feature
git merge main

# 방법 2: Rebase (히스토리 깔끔)
git checkout feature/my-feature
git rebase main

# 충돌 해결 후
git rebase --continue
```

---

## 체크리스트

### 커밋 전

- [ ] `git diff`로 변경사항 확인
- [ ] 불필요한 파일은 제외 (.env, node_modules 등)
- [ ] 의미 있는 커밋 메시지 작성
- [ ] 한 커밋에 하나의 논리적 변경

### PR 전

- [ ] 최신 main과 충돌 없음
- [ ] 테스트 통과
- [ ] Linter 통과
- [ ] 리뷰어 지정
- [ ] 명확한 PR 설명

### Merge 전

- [ ] 리뷰 승인 받음
- [ ] CI/CD 통과
- [ ] 충돌 해결 완료

---

## 마무리 원칙

> "Git은 타임머신이다. 미래의 나와 팀을 위해 명확한 기록을 남겨라."

- **작게 커밋**: 하나의 변경사항에 하나의 커밋
- **명확한 메시지**: 왜 변경했는지 설명
- **자주 푸시**: 로컬에만 쌓아두지 말기
- **main은 신성**: 항상 배포 가능한 상태 유지
- **소통**: PR에 충분한 설명과 스크린샷

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gdm0714) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
