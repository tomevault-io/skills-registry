---
name: git-advanced
description: 인터랙티브 리베이싱(interactive rebasing), 충돌 해결, 히스토리 조작, 버그 추적을 위한 bisect, cherry-picking, reflog 복구 및 브랜치 관리 전략을 포함한 고급 Git 작업 및 워크플로우입니다. 사용 사례: (1) 인터랙티브 리베이싱 및 커밋 정리, (2) 복잡한 머지 충돌 해결, (3) 버그 추적을 위한 Git bisect, (4) 히스토리 재작성 및 정리, (5) 브랜치 전략 구현 (Git Flow, trunk-based), (6) reflog를 이용한 유실된 커밋 복구 Use when this capability is needed.
metadata:
  author: icartsh
---

# Advanced Git Operations

## 개요 (Overview)

복잡한 버전 관리 시나리오를 위한 고급 Git 워크플로우를 마스터하세요. 이 SKILL은 기본적인 커밋과 머지를 넘어 인터랙티브 리베이싱, 고급 충돌 해결, 히스토리 조작 및 전략적 브랜치 관리를 포함한 정교한 작업들을 다룹니다.

다음을 수행할 때 이 SKILL을 사용하세요:
- 코드 리뷰 전 지저분한 커밋 히스토리 정리
- 복잡한 머지 충돌의 전략적 해결
- 이진 탐색(bisect)을 통한 버그 추적
- 유실된 커밋 복구 또는 실수 되돌리기
- 팀 브랜치 전략 구현
- 안전한 히스토리 재작성

## 핵심 역량 (Core Capabilities)

### 인터랙티브 리베이싱 (Interactive Rebasing)
- 논리적 히스토리를 위한 커밋 순서 재배치
- 여러 커밋을 하나로 합치기 (Squash)
- 과거 커밋 메시지 수정
- 커밋을 더 작은 조각으로 분리
- 히스토리에서 원치 않는 커밋 제거

### 충돌 해결 (Conflict Resolution)
- 전략적인 머지 충돌 처리
- 3-way 머지 이해 및 활용
- Ours vs. Theirs 전략 선택
- 충돌 마커 해석
- 충돌 중 파일의 부분적 스테이징(staging)

### Git Bisect
- 버그가 도입된 지점을 찾기 위한 이진 탐색
- 스크립트를 이용한 자동 bisect
- Good/Bad 커밋 식별
- 회귀(regressions) 버그의 효율적인 디버깅

### 히스토리 조작 (History Manipulation)
- 안전한 커밋 수정 (Amend)
- Filter-branch 작업
- 대용량 파일을 위한 BFG Repo-Cleaner 활용
- 주의 깊은 히스토리 재작성
- 작성자 정보 및 날짜 보존

### 브랜치 전략 (Branch Strategies)
- Git Flow 워크플로우
- Trunk-based 개발
- Feature 브랜치 워크플로우
- 릴리스(Release) 브랜치 관리
- 핫픽스(Hotfix) 절차

## 빠른 명령 참조 (Quick Command Reference)

### 인터랙티브 리베이즈 (Interactive Rebase)
```bash
# 최근 5개 커밋을 인터랙티브하게 리베이즈
git rebase -i HEAD~5

# main 브랜치를 대상으로 리베이즈
git rebase -i main

# 충돌 해결 후 계속 진행
git rebase --continue

# 중단하고 원래 상태로 복구
git rebase --abort
```

### 충돌 해결 (Conflict Resolution)
```bash
# '우리 것'(현재 브랜치) 선택
git checkout --ours <file>

# '그들 것'(들어오는 브랜치) 선택
git checkout --theirs <file>

# 충돌 발생 파일 목록 확인
git diff --name-only --diff-filter=U

# 해결됨으로 표시
git add <file>
```

### Git Bisect
```bash
# bisect 세션 시작
git bisect start
git bisect bad
git bisect good <commit-hash>

# 테스트 스크립트로 자동화
git bisect run ./test-script.sh

# bisect 세션 종료
git bisect reset
```

### Cherry-Pick
```bash
# 특정 커밋 적용
git cherry-pick <commit-hash>

# 커밋하지 않고 cherry-pick (스테이징만 수행)
git cherry-pick -n <commit-hash>

# 커밋 범위 cherry-pick
git cherry-pick A^..B
```

### Reflog 복구 (Reflog Recovery)
```bash
# reflog 히스토리 확인
git reflog

# 유실된 커밋 복구
git checkout -b recovery <commit-hash>

# 이전 상태로 리셋
git reset --hard HEAD@{2}
```

## 핵심 워크플로우 (Core Workflows)

### 1. 인터랙티브 리베이즈 워크플로우 (Interactive Rebase Workflow)

**사용 시기:**
- 푸시 전 지저분한 커밋 히스토리 정리
- "WIP" 또는 "fix typo" 커밋 병합
- 논리적 흐름에 맞춰 커밋 순서 재조정
- 커다란 커밋을 전문적인 변경 사항들로 조각내기

**단계:**

```bash
# 1. 최근 N개 커밋에 대해 인터랙티브 리베이즈 시작
git rebase -i HEAD~5

# 커밋 목록이 포함된 인터랙티브 에디터가 열림
# 히스토리 재구성을 위해 명령어를 수정
```

**리베이즈 명령어:**
- `pick` (p): 커밋 그대로 유지
- `reword` (r): 커밋은 유지하되 메시지 수정
- `edit` (e): 커밋을 유지하되 수정을 위해 멈춤 (amend 가능)
- `squash` (s): 이전 커밋과 합치고 메시지 유지
- `fixup` (f): 이전 커밋과 합치되 메시지는 버림
- `drop` (d): 커밋을 완전히 제거

**예시:**
```bash
# 수정 전
pick abc1234 Add feature X
pick def5678 Fix typo
pick ghi9012 WIP commit
pick jkl3456 Update documentation

# 정리 후
pick abc1234 Add feature X
fixup def5678 Fix typo
drop ghi9012 WIP commit
reword jkl3456 Update documentation
```

**안전 팁:**
- 이미 공유 브랜치에 푸시된 커밋은 리베이즈하지 마세요.
- 백업 브랜치 생성: `git branch backup`
- 문제가 생기면 `git reflog`를 사용하세요.
- 포스 푸시(force push) 시 주의: `git push --force-with-lease`

상세 예시와 고급 기법은 `examples/interactive_rebase.md`를 참조하세요.

### 2. 충돌 해결 워크플로우 (Conflict Resolution Workflow)

**충돌 마커 이해하기:**
```
<<<<<<< HEAD (현재 변경 사항)
현재 브랜치의 코드
=======
머지하려는 브랜치의 코드
>>>>>>> branch-name (들어오는 변경 사항)
```

**해결 프로세스:**

```bash
# 1. 충돌 발생 확인
git status

# 2. 해결 전략 선택:

# 전략 A: 수동 해결
vim <file>  # 마커 사이의 내용 수정
git add <file>

# 전략 B: 한쪽 선택
git checkout --ours <file>    # 내 것 유지
git checkout --theirs <file>  # 상대 것 유지
git add <file>

# 전략 C: 머지 도구 사용
git mergetool

# 3. 작업 계속 진행
git merge --continue
# 또는
git rebase --continue
```

**3-Way Diff 확인:**
```bash
# '내가' 변경한 내용 확인
git diff --ours <file>

# '그들이' 변경한 내용 확인
git diff --theirs <file>

# 공통 조상(Base) 확인
git diff --base <file>
```

공통적인 충돌 패턴과 해결책은 `examples/conflict_resolution.md`를 참조하세요.

### 3. 버그 추적을 위한 Git Bisect (Git Bisect for Bug Hunting)

**시나리오:** 현재 버전에 버그가 있을 때, 어떤 커밋에서 도입되었는지 찾습니다.

**수동 Bisect:**
```bash
# 1. bisect 시작
git bisect start

# 2. 현재 상태를 'bad'로 마킹
git bisect bad

# 3. 버그가 없었던 과거 커밋을 'good'으로 마킹
git bisect good v1.0.0

# 4. 코드 테스트 (Git이 중간 지점 커밋을 체크아웃함)
# 앱을 실행하여 버그 존재 여부 확인

# 5. 결과 마킹
git bisect bad   # 버그가 있는 경우
git bisect good  # 버그가 없는 경우

# Git이 첫 번째 bad 커밋을 찾을 때까지 반복

# 6. bisect 종료
git bisect reset
```

**자동화된 Bisect:**
```bash
# 테스트 스크립트 작성 (test.sh)
#!/bin/bash
npm test
exit $?

# 자동화된 bisect 실행
git bisect start
git bisect bad
git bisect good v1.0.0
git bisect run ./test.sh

# Git이 자동으로 bad 커밋을 찾아냄
git bisect reset
```

### 4. 브랜치 관리 (Branch Management)

**빠른 정리:**
```bash
# 헬퍼 스크립트 사용
bash scripts/git_helper.sh cleanup-branches

# 또는 수동 정리
git branch --merged main | grep -v "\*\|main\|develop" | xargs git branch -d
git fetch --prune
```

**오래된 브랜치 감지:**
```bash
# 마지막 커밋 날짜와 함께 브랜치 표시
for branch in $(git branch -r | grep -v HEAD); do
    echo -e "$(git show --format="%ci %cr" $branch | head -n 1)\t$branch"
done | sort -r
```

상세한 브랜치 관리 전략은 `references/branch-management.md`를 참조하세요.

### 5. Reflog를 이용한 복구 (Reflog Recovery)

**삭제된 브랜치 복구:**
```bash
# reflog 확인
git reflog

# 브랜치가 삭제된 시점의 커밋 찾기
# 브랜치 복구
git checkout -b feature-x <commit-hash>
```

**잘못된 Reset 되돌리기:**
```bash
# 실수로 실행함: git reset --hard HEAD~5

# reflog 확인
git reflog

# 이전 상태로 복구
git reset --hard HEAD@{1}
```

**유실된 커밋 찾기:**
```bash
# 연결이 끊긴(dangling) 커밋 찾기
git fsck --lost-found

# 찾은 커밋을 cherry-pick
git cherry-pick <lost-commit-hash>
```

포괄적인 복구 기법은 `references/reflog-recovery.md`를 참조하세요.

## 브랜치 전략 구현 (Branch Strategy Implementation)

이 SKILL은 다양한 브랜치 전략 구현을 지원합니다. 팀의 필요에 따라 선택하세요:

### Git Flow
**적합한 사례:** 정기적인 릴리스 일정이 있고, 여러 운영 버전을 유지해야 하는 프로젝트

**브랜치 유형:**
- `main`: 운영 환경에 배포 가능한 코드
- `develop`: 개발 통합 브랜치
- `feature/*`: 새로운 기능 개발
- `release/*`: 릴리스 준비
- `hotfix/*`: 운영 환경 긴급 수정

**빠른 시작:**
```bash
# 기능 개발 시작
git checkout develop
git checkout -b feature/user-auth
# 작업 완료 후
git checkout develop
git merge --no-ff feature/user-auth
```

### Trunk-Based Development
**적합한 사례:** 지속적 배포(CI/CD), 빠른 속도를 지향하는 팀

**원칙:**
- 단일 메인 브랜치 사용
- 짧은 수명의 피처 브랜치 (< 1일)
- 잦은 통합
- 미완성 작업을 위한 기능 플래그(Feature flags) 사용

**빠른 시작:**
```bash
# 짧은 수명의 브랜치 생성
git checkout main
git checkout -b feature/quick-fix
# 같은 날 작업 완료 및 머지
git checkout main
git merge feature/quick-fix
```

전체 브랜치 전략 워크플로우는 `examples/branch_strategies.md`를 참조하세요.

## 모범 사례 (Best Practices)

### 리베이즈 전
- 백업 브랜치 생성: `git branch backup`
- 작업 디렉토리가 깔끔한지 확인: `git status`
- 커밋 히스토리 확인: `git log --oneline`
- 공개/공유 브랜치는 절대 리베이즈하지 말 것

### 충돌 발생 시
- 충돌의 양쪽 변경 사항을 모두 이해할 것
- 해결 후 철저히 테스트할 것
- 의미 있는 머지 커밋 메시지 작성
- 복잡한 해결 과정은 문서화할 것

### 브랜치 관리
- 머지된 브랜치는 즉시 삭제할 것
- 설명적인 브랜치 이름 사용: `feature/user-login`
- 브랜치는 목적에 집중하고 짧게 유지할 것
- main 브랜치와 정기적으로 동기화할 것

### 히스토리 관리
- 푸시 전 "fixup" 커밋들은 합칠(squash) 것
- 명확하고 설명적인 커밋 메시지 작성
- 커밋은 원자적으로 유지 (하나의 논리적 변경 사항)
- 가능한 경우 Conventional Commit 형식을 따를 것

포괄적인 모범 사례는 `references/best-practices.md`를 참조하세요.

## 트러블슈팅 (Troubleshooting)

### 리베이즈 충돌
```bash
# 충돌이 너무 복잡한 경우
git rebase --abort

# 다른 방식으로 다시 시작
git merge --no-ff <branch>
```

### 유실된 커밋
```bash
# 항상 reflog부터 확인
git reflog

# 커밋을 찾아 복구
git checkout -b recovery <commit-hash>
```

### Detached HEAD 상태
```bash
# 현재 상태에서 브랜치 생성
git checkout -b new-branch-name

# 또는 원래 브랜치로 복구
git checkout main
```

### 리베이즈 후 푸시 실패 (Failed Push)
```bash
# 확실한 경우에만, 그리고 공유 브랜치가 아닐 때만 실행
git push --force-with-lease

# 더 안전한 방식: 새 브랜치 생성
git checkout -b feature-v2
git push origin feature-v2
```

상세한 트러블슈팅은 `references/troubleshooting.md`를 참조하세요.

## 추가 자료

### 상세 가이드
- `examples/interactive_rebase.md`: 시나리오별 단계적 리베이즈 예시
- `examples/conflict_resolution.md`: 공통 충돌 패턴 및 해결책
- `examples/branch_strategies.md`: 전체 Git Flow 및 Trunk-based 워크플로우

### 참조 문서
- `references/branch-management.md`: 브랜치 정리 자동화 및 전략
- `references/reflog-recovery.md`: 복구 기법 및 히스토리 재작성
- `references/best-practices.md`: 포괄적인 모범 사례 및 품질 표준
- `references/troubleshooting.md`: 일반적인 이슈 및 긴급 복구

### 헬퍼 스크립트
- `scripts/git_helper.sh`: 브랜치 정리 및 충돌 해결 유틸리티

## 빠른 참조 명령 (Quick Reference Commands)

### 필수 명령
```bash
# 인터랙티브 리베이즈
git rebase -i HEAD~N

# 작업 중단
git rebase --abort
git merge --abort
git cherry-pick --abort

# 히스토리 확인
git log --oneline --graph --all
git reflog

# 충돌 해결
git checkout --ours <file>
git checkout --theirs <file>

# 복구
git fsck --lost-found
git reflog

# 정리
git branch --merged | xargs git branch -d
git fetch --prune
```

### 안전 제일 (Safety First)
1. 히스토리 재작성 전 항상 백업: `git branch backup`
2. 공유 브랜치에 포스 푸시 금지: `--force-with-lease` 사용
3. 충돌 해결 후 반드시 테스트: 해결이 올바르다고 가정하지 말 것
4. 복잡한 작업은 기록: 머지 커밋에 주석 남기기
5. reflog 활용: 30일 이상 당신의 안전망이 되어줍니다.

---

깔끔한 히스토리를 유지하고, 충돌을 효율적으로 해결하며, 복잡한 개발 워크플로우를 자신 있게 관리하기 위해 이 고급 Git 작업들을 마스터하세요.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/icartsh) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
