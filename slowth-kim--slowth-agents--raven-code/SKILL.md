---
name: raven-code
description: Developer 에이전트의 구현 프로세스. Feature 구현, 세션 관리, 커밋 워크플로우의 세부 단계를 정의합니다. Use when this capability is needed.
metadata:
  author: slowth-kim
---

# Raven Code - Implementation Process

💻 Coding Agent의 세부 구현 프로세스입니다.

## Main Menu

```
💻 Coding Agent - Developer

활성 세션: {있음/없음}
Focus 태스크: {n}개

[1] impl   - Feature 구현 시작
[2] resume - 중단된 구현 재개
[3] fix    - Quick fix (PRD 없이)
[4] status - 현재 상태 확인
[x] 종료
```

---

## impl - Feature 구현

### Step 1: 세션 확인

이전 세션이 있으면:
```
이전 세션: {task_name}
진행: {completed}/{total} 완료
[r] 이어서 / [n] 새로 시작
```

### Step 2: 태스크 선택

`.raven/tasks/focus/`에서 PRD가 있는 태스크 목록 표시.

없으면:
```
Focus에 task가 없습니다.
[n] next/에서 선택
[d] 직접 설명
```

### Step 3: 구현 계획 생성

PRD 분석 후 태스크 분해:
- 필요한 변경 사항
- 파일 수정/생성
- 의존성과 순서

```
구현 계획:
1. {subtask_1}
2. {subtask_2}
3. {subtask_3}

맞나요? [y] 예 / [e] 수정
```

`working-init`으로 세션 초기화, 계획 저장.

### Step 4: 구현 실행 (테스트 통합)

각 서브태스크마다:
```
▶ Task {n}/{total}: {subtask_name}
```

**4.1 구현**
- 서브태스크 구현
- diff 또는 요약 표시

**4.2 테스트 실행 (자동)**
- 관련 테스트 파일 감지 및 실행
- 테스트 없으면 스킵

```
테스트 결과: ✅ {passed}/{total} passed
```

**4.3 테스트 실패 시**
```
❌ 테스트 실패:
- {test_name}: {error}

[f] 즉시 수정
[s] 스킵하고 계속
[p] 일시정지
```

- `f`: 실패 원인 분석 후 수정, 재테스트
- `s`: 나중에 수정 (warning 기록)
- `p`: 상태 저장 후 종료

**4.4 진행**
- `working-update`로 상태 업데이트

```
[c] 다음 서브태스크 / [p] 일시정지
```

### Step 5: 통합 검증 (자체 QA)

**5.1 전체 테스트 실행**
```bash
# 프레임워크 자동 감지
npm test / pytest / go test ./... / cargo test
```

```
전체 테스트:
✅ Passed: {pass}
❌ Failed: {fail}
```

**5.2 PRD 수락 기준 검증**
PRD에서 수락 기준 추출 후 자체 검증:
```
수락 기준 검증:
✅ 기준 1: 충족
✅ 기준 2: 충족
❌ 기준 3: 미충족 - {reason}
```

**5.3 결과 요약**
```
검증 결과: {pass_rate}% 통과

[c] 완료 → 커밋 (모두 통과)
[f] 미충족 항목 수정
[q] 심층 QA 요청 (Tester 호출)
```

### Step 6: 커밋

```
커밋 메시지: "{message}"
[y] 커밋 / [e] 수정 / [n] 취소
```

### Step 7: 완료 처리

검증 통과 시:
```
🎉 구현 및 검증 완료!

다음 단계:
[d] 태스크 완료 (done/으로 이동)
[n] 다음 태스크
[g] GTD로 돌아가기
```

- `d`: 태스크를 done/으로 이동, `working.json` 초기화
- `n`: focus/에서 다음 태스크 선택
- `g`: GTD Agent로 핸드오프

---

## resume - 세션 재개

1. `working-load`로 상태 로드
2. 진행 상황 표시:
   ```
   Task: {task_name}
   진행: {completed}/{total}

   ✅ 완료: {completed_subtasks}
   ▶ 다음: {next_subtask}
   ```
3. git status 확인
4. impl Step 4로 점프

---

## fix - Quick Fix

1. 수정 내용 파악
2. 변경 수행, diff 표시
3. 커밋:
   ```
   fix: {message}

   🪶 Raven Coding Agent
   ```

---

## status - 현재 상태

```bash
git status --short
git log --oneline -5
```

활성 세션 있으면 진행 상황 표시.

---

## verify - 자체 검증

구현 완료 후 독립적으로 검증 실행:

1. PRD 로드 및 수락 기준 추출
2. 전체 테스트 실행
3. 각 수락 기준 검증:
   ```
   "{criteria}"
   [p] Pass ✅
   [f] Fail ❌
   [s] Skip
   ```
4. 결과 요약 및 다음 단계 제안

---

## Test Framework Detection

자동 감지 순서:
```bash
# 1. package.json 확인
grep -E "(jest|mocha|vitest)" package.json

# 2. Python 프로젝트
ls pytest.ini pyproject.toml setup.py

# 3. Go 프로젝트
ls go.mod

# 4. Rust 프로젝트
ls Cargo.toml
```

| Framework | Test Command |
|-----------|--------------|
| Jest | `npm test` |
| Vitest | `npx vitest run` |
| Pytest | `pytest` |
| Go | `go test ./...` |
| Cargo | `cargo test` |

---

## Implementation Guidelines

1. **프로젝트 컨벤션 준수** - CLAUDE.md 확인
2. **점진적 구현** - 한 번에 한 파일/함수
3. **구현 후 즉시 테스트** - 버그 조기 발견
4. **에러 처리** - edge case 고려
5. **테스트 가능성** - 의존성 주입 고려

---

## Commit Format

```
{type}: {description}

{body}

🪶 Raven Coding Agent
```

Types: `feat`, `fix`, `refactor`, `docs`, `test`, `chore`

---

## BMAD Integration

- **시작**: `working-load`, `belief-load`
- **진행 중**: `working-update`, `decision-log`
- **종료**: `dialogue-save`, `handoff-write`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/slowth-kim) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
