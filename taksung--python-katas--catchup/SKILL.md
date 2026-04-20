---
name: catchup
description: Git 저장소의 변경사항을 추적하고 요약합니다. 미커밋 코드, 최근 커밋, 커밋 히스토리를 확인할 때 사용하세요. catchup, 변경사항, git diff, 커밋 히스토리, 작업 내용 파악 등의 키워드에 반응합니다. Use when this capability is needed.
metadata:
  author: taksung
---

# Catchup - Git 변경사항 추적 스킬

현재 작업 중인 코드의 변경사항을 빠르게 파악하여 컨텍스트를 제공하는 스킬입니다.

## 주요 기능

모든 기능은 `./scripts/git-helper.sh`를 통해 접근합니다.

| 작업 | 명령어 | 설명 |
|---|---|---|
| **미커밋 변경사항** | `./scripts/git-helper.sh status` | Staged + Unstaged 변경사항 확인 |
| **직전 커밋** | `./scripts/git-helper.sh last` | 가장 최근 커밋 상세 정보 |
| **최근 커밋 목록** | `./scripts/git-helper.sh log [n]` | 최근 n개 커밋 목록 (기본값: 10) |
| **커밋 범위 비교** | `./scripts/git-helper.sh diff <start> <end>` | 두 커밋 사이의 변경사항 |

### 필터링 옵션

명령어 앞에 다음 옵션을 추가할 수 있습니다:

| 옵션 | 설명 | 사용 예시 |
|---|---|---|
| `--code-only` | 코드 파일만 표시 (문서, 설정 파일 제외) | `./scripts/git-helper.sh --code-only status` |
| `--no-meta-commits` | 메타 커밋 제외 (docs, prompt, config 등) | `./scripts/git-helper.sh --no-meta-commits log` |

**필터링 대상 (--code-only):**
- 제외: `docs/*`, `.claude/*`, `.gemini/*`, `*.md`, `*.toml`, `.gitignore`, `.katarc`
- 포함: 실제 코드 파일 (`.py`, `.sh` 등)

**필터링 커밋 (--no-meta-commits):**
- 제외: `^prompt`, `^📌`, `^docs`, `^📝`, `^config`, `^🔧`, `^chore`, `^style`, `^refactor`, `^test`, `^build`, `^ci`

---

## 사용 예시

### 예시 1: 현재 작업 파악

**사용자 요청:**
> "지금까지 뭐 작업했는지 catchup 해줘"

**스킬 동작:**
```bash
# 미커밋 변경사항
./scripts/git-helper.sh status

# 최근 커밋
./scripts/git-helper.sh last
```

### 예시 2: 코드만 확인

**사용자 요청:**
> "문서 빼고 코드 변경사항만 보여줘"

**스킬 동작:**
```bash
./scripts/git-helper.sh --code-only status
```

### 예시 3: 최근 히스토리 확인

**사용자 요청:**
> "최근에 어떤 작업들을 했는지 커밋 목록 보여줘"

**스킬 동작:**
```bash
./scripts/git-helper.sh log 10
```

### 예시 4: 의미있는 커밋만 확인

**사용자 요청:**
> "문서 커밋 빼고 실제 기능 커밋만 보여줘"

**스킬 동작:**
```bash
./scripts/git-helper.sh --no-meta-commits log 20
```

### 예시 5: 특정 범위 조사

**사용자 요청:**
> "커밋 abc123부터 def456까지 뭐가 바뀌었는지 보여줘"

**스킬 동작:**
```bash
./scripts/git-helper.sh diff abc123 def456
```

### 예시 6: 코드만 + 메타 커밋 제외

**사용자 요청:**
> "실제 기능 구현 코드만 최근 10개 커밋 보여줘"

**스킬 동작:**
```bash
./scripts/git-helper.sh --code-only --no-meta-commits log 10
```

---

## 출력 형식

### status 명령어
```
📝 Unstaged changes:
 hidden-number/domain/game.py | 10 +++++-----

✅ Staged changes:
 hidden-number/tests/test_game.py | 5 +++++
```

### last 명령어
```
🔖 Latest commit:
a760d78 - 📝docs : 드라이버 가이드문서 업데이트 (2 hours ago)

 docs/driver-guide.md | 25 +++++++++++++++++++++++++
```

### log 명령어
```
📋 Recent 10 commits:
a760d78 📝docs : 드라이버 가이드문서 업데이트
ae467b4 📌prompt : update 드라이버 가이드
2d257c1 📌prompt : update 드라이버 가이드
```

### diff 명령어
```
📊 Commit range: abc123..def456

abc124 ✨feat : Game 클래스 구현
def456 ✅test : Game 테스트 추가

📝 Detailed changes per commit:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔖 abc124 - ✨feat : Game 클래스 구현
👤 takgyun (2 hours ago)

 hidden-number/domain/game.py | 50 ++++++++++++++++++++++++++++++++
```

---

## 주의사항

- Git 저장소 루트에서 실행되어야 합니다
- 한글 파일명/커밋 메시지가 포함된 경우 UTF-8 인코딩 자동 설정됨
- 큰 diff는 `--stat`으로 요약하여 컨텍스트 절약
- 필요시에만 전체 diff 내용을 제공하도록 선택적으로 사용

## 한글 인코딩 설정

스크립트 내부에서 자동으로 설정됩니다:
```bash
export LC_ALL=C.UTF-8
GIT_OPTS="-c core.quotepath=false"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/taksung) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
