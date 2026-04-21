---
name: high-quality-commit
description: 코드 변경 사항을 적절한 git 커밋 전략으로 git commit한다. 기본적으로는 기존 git 커밋에 squash 전략을 적용하며, 필요에 따라 브랜치 전체의 git 커밋 히스토리를 재구성한다. 구현 완료 시점이나 사용자가 git commit을 요청했을 때 사용한다. Use when this capability is needed.
metadata:
  author: gaebalai
---

# High Quality Commit

이 스킬은 코드 변경 사항을 **고품질 git 커밋**으로 기록하기 위한 포괄적인 가이드를 제공한다.

## Instructions

### Step 1: 브랜치 및 git 커밋 히스토리 확인

아래 명령어로 현재 상태를 확인한다:

```bash
git status
git log --oneline --graph origin/main..HEAD
```

확인 사항:
- 현재 브랜치 이름
- main 브랜치 기준으로 몇 개의 git 커밋이 쌓여 있는지
- 각 git 커밋의 내용과 커밋 단위(그레인)

### Step 2: git 커밋 전략 판단

아래 기준에 따라 git 커밋 전략을 선택한다:

#### 전략 A: Squash (기본 전략)

다음 조건을 만족하는 경우 기존 git 커밋에 squash 한다:

- 브랜치에 이미 git 커밋이 존재한다
- 변경 내용이 기존 git 커밋과 동일한 주제 또는 기능에 속한다
- git 커밋을 분리해야 할 합리적인 이유가 없다

**실행 방법:**

```bash
git add -A
git commit --amend
```

git 커밋 메시지를 적절히 수정한다.

#### 전략 B: 신규 git 커밋

다음에 해당하는 경우 신규 git 커밋을 생성한다:

- 브랜치에서 첫 번째 git 커밋인 경우
- 기존 git 커밋과 명확히 구분되는 독립적인 변경
- git 커밋을 분리하는 것이 히스토리 이해에 도움이 되는 경우

**실행 방법:**

```bash
git add -A
git commit
```

#### 전략 C: Interactive Rebase (git 커밋 재구성)

다음의 경우 브랜치 전체 git 커밋을 재구성한다:

- 여러 개의 작은 git 커밋을 논리적인 단위로 정리하고 싶은 경우
- git 커밋 순서를 변경하고 싶은 경우
- 불필요한 git 커밋을 제거하고 싶은 경우
- git 커밋 히스토리를 의미 있는 단위로 재편성하고 싶은 경우

**실행 방법:**

```bash
git rebase -i origin/main
```

에디터에서 다음 작업을 수행한다:
- `pick`: git 커밋을 그대로 유지
- `squash` 또는 `s`: 이전 git 커밋과 병합
- `reword` 또는 `r`: git 커밋 메시지 수정
- 줄 순서를 변경해 git 커밋 순서 변경

### Step 3: git 커밋 메시지 가이드라인

git 커밋 메시지는 아래 형식을 따른다:

```
<type>: <subject>

<body>

<footer>
```

**Type:**
- `feat`: 신규 기능
- `fix`: 버그 수정
- `refactor`: 리팩토링
- `test`: 테스트 추가 또는 수정
- `docs`: 문서 변경
- `chore`: 빌드 프로세스, 도구 변경 등

**Subject:**
- 50자 이내
- 명령형으로 작성 (예: "Added"가 아닌 "Add")
- 문장 끝에 마침표를 붙이지 않는다

**Body(선택):**
- 변경의 이유와 배경을 설명
- 무엇을 바꿨는지가 아니라 왜 바꿨는지를 기술
- 한 줄당 72자 기준으로 줄바꿈

**Footer(선택):**
- Issue 번호 참조 (예: `Closes #123`)
- Breaking changes 설명

### Step 4: git commit 이후 확인

git commit 후 아래를 확인한다:

```bash
git log -1 --stat
git status
```

- git 커밋이 올바르게 생성되었는지
- 의도한 파일이 모두 포함되었는지
- git 커밋 메시지가 적절한지

## 중요 주의 사항

1. **main 브랜치에서 실행 금지**: main 브랜치에 직접 git commit 하지 않는다.
2. **주석 금지**: 코드 내 설명용 주석은 남기지 않는다.
3. **원자적 git 커밋**: 각 git 커밋은 독립적으로 의미를 가져야 한다.
4. **일관성 유지**: 프로젝트의 기존 git 커밋 스타일을 따른다.

## 전략 선택 플로우차트

```
브랜치에 git 커밋이 있는가?
  ├─ No → 신규 git 커밋 생성
  └─ Yes → 변경 내용이 기존 git 커밋과 같은 주제인가?
      ├─ Yes → Squash（git commit --amend）
      └─ No → git 커밋을 분리할 합리성이 있는가?
          ├─ Yes → 신규 git 커밋 생성
          └─ 히스토리를 정리하고 싶다 → Interactive Rebase
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gaebalai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
