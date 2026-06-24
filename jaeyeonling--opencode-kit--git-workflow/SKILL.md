---
name: git-workflow
description: Git 작업 흐름, 커밋 메시지, 브랜치 전략 가이드 Use when this capability is needed.
metadata:
  author: jaeyeonling
---

# Git Workflow Guide

## 커밋 메시지 형식

### Conventional Commits

```
<type>(<scope>): <description>

[optional body]

[optional footer(s)]
```

### Type 종류

| Type | 설명 | 예시 |
|------|------|------|
| `feat` | 새로운 기능 추가 | feat(auth): add OAuth2 login |
| `fix` | 버그 수정 | fix(cart): correct price calculation |
| `docs` | 문서 변경 | docs: update API documentation |
| `style` | 코드 포맷팅 | style: fix indentation |
| `refactor` | 리팩토링 | refactor(user): extract validation logic |
| `test` | 테스트 추가/수정 | test(api): add unit tests for endpoints |
| `chore` | 빌드, 설정 등 | chore: update dependencies |
| `perf` | 성능 개선 | perf(query): optimize database queries |
| `ci` | CI 설정 변경 | ci: add GitHub Actions workflow |

### 좋은 커밋 메시지 예시

```text
feat(payment): add Stripe payment integration

- Add Stripe SDK and configuration
- Implement checkout session creation
- Add webhook handlers for payment events

Closes #123
```

### 피해야 할 커밋 메시지

- `fix bug`
- `update code`
- `WIP`
- `asdf`

## 브랜치 전략

### Git Flow

```text
main (production)
  └── develop
        ├── feature/add-login
        ├── feature/update-dashboard
        └── bugfix/fix-payment-error
```

### 브랜치 이름 규칙

```text
feature/<feature-name>    # 새 기능
bugfix/<bug-description>  # 버그 수정
hotfix/<issue>            # 긴급 수정
release/<version>         # 릴리스 준비
```

## PR (Pull Request) 가이드

### PR 템플릿

```markdown
## 변경 사항
- 

## 변경 이유
- 

## 테스트
- [ ] 단위 테스트 추가/수정
- [ ] 통합 테스트 확인
- [ ] 수동 테스트 완료

## 스크린샷 (UI 변경 시)


## 관련 이슈
Closes #
```

### PR 체크리스트

1. 변경 범위가 적절한가? (너무 크면 분리)
2. 테스트가 통과하는가?
3. 코드 리뷰 준비가 되었는가?
4. 문서 업데이트가 필요한가?

## 자주 사용하는 Git 명령어

```bash
# 브랜치 생성 및 이동
git checkout -b feature/new-feature

# 변경사항 확인
git status
git diff

# 커밋
git add -p  # 변경사항 선택적 추가
git commit -m "feat: add new feature"

# 리베이스
git fetch origin
git rebase origin/main

# 스쿼시 (여러 커밋 합치기) - 터미널에서 직접 실행
git rebase -i HEAD~3

# 실수 복구
git reset --soft HEAD~1  # 마지막 커밋 취소 (변경사항 유지)
git stash                # 임시 저장
git stash pop            # 임시 저장 복구
```

## 충돌 해결

1. 충돌 파일 확인: `git status`
2. 충돌 마커 확인 및 수정
3. 수정 완료 후: `git add <file>`
4. 계속 진행: `git rebase --continue`

## Git 작업 체크리스트

### 커밋 전
- [ ] 변경사항이 하나의 논리적 단위인가?
- [ ] 불필요한 파일이 포함되지 않았는가?
- [ ] 커밋 메시지가 Conventional Commits 형식인가?
- [ ] 민감정보가 포함되지 않았는가?

### PR 생성 전
- [ ] 브랜치가 최신 main/develop과 동기화되었는가?
- [ ] 모든 테스트가 통과하는가?
- [ ] 코드 리뷰 준비가 되었는가?
- [ ] PR 설명이 충분한가?

### 머지 전
- [ ] 리뷰어 승인을 받았는가?
- [ ] CI/CD 파이프라인이 통과했는가?
- [ ] 충돌이 해결되었는가?

## 관련 스킬

- `documentation`: PR 설명 작성
- `code-quality`: 커밋 전 코드 리뷰

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jaeyeonling) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
