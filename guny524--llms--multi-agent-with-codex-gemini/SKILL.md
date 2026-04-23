---
name: multi-agent-with-codex-gemini
description: gemini와 codex 두 agent를 parallel로 실행하여 편향 없는 다각도 분석. code review, documentation validation, architecture verification 등 프로젝트 전체 검증이 필요할 때 사용. 동일한 instruction을 두 agent에게 독립적으로 실행시켜 결과를 교차 검증. Use when this capability is needed.
metadata:
  author: guny524
---

# Multi-Agent with Codex & Gemini
gemini와 codex를 parallel로 실행하여 single-agent bias 제거 및 diverse perspective 확보

## 1. 전제조건
- gemini CLI 설치 및 인증
- codex CLI 설치 및 인증
- 두 agent를 foreground로 parallel 실행 (`&` 사용 금지)

## 2. 핵심 기능
동일한 분석 작업을 gemini와 codex에 독립적으로 실행 후 결과 aggregation

### 2-1. 사용 시점
- Project-wide documentation consistency validation
- Code review (logic errors, inefficiencies, anti-patterns)
- Architectural decision verification
- Code vs documentation mismatch 찾기
- TODO 문서와 실제 구현 cross-reference

### 2-2. Parallel Execution 방법 (중요!)

**절대 규칙: Task tool로 3개 subagent를 병렬 실행해야 한다.**

Bash로 직접 CLI를 호출하면 **순차 실행**된다!

#### 올바른 구조
```
main agent (Claude)
├─ Task 1 (parallel, run_in_background=true)
│   └─ Opus subagent: 직접 분석 수행
├─ Task 2 (parallel, run_in_background=true)
│   └─ subagent: Bash로 `codex exec "prompt"` 호출
└─ Task 3 (parallel, run_in_background=true)
    └─ subagent: Bash로 `gemini -p "prompt"` 호출

→ 3개 완료 후, 필요시 main agent가 추가 검토
```

#### 잘못된 구조 (순차 실행됨!)
```
main agent (Claude)
├─ Bash: codex exec "prompt"  ← 완료까지 대기
└─ Bash: gemini -p "prompt"   ← 그 다음 실행
```

#### 실행 방법
하나의 응답에서 Task tool 3개를 동시 호출한다:

1. **Task 1 (Opus)**: subagent_type="general-purpose", run_in_background=true
   - prompt: "[review instruction]"
   - Opus subagent가 직접 분석 수행

2. **Task 2 (Codex)**: subagent_type="general-purpose", run_in_background=true
   - prompt: "Run `codex exec` with this prompt: [review instruction]"

3. **Task 3 (Gemini)**: subagent_type="general-purpose", run_in_background=true
   - prompt: "Run `gemini -p` with this prompt: [review instruction]"

**핵심**: 3개 Task를 **한 번의 응답에서 동시에 호출** → 병렬 실행 → 완료 후 main agent가 결과 aggregation (필요시 추가 검토)

## 3. Instruction Structure
세 agent 모두 동일한 instruction 제공

### 3-1. 기본 구조
```
[Task Description]

### 1. Context
- !git log --oneline -10
- !git diff --cached
- @README.md @todos/TODO_*.md

### 2. Exploration
- @component1/README.md @component2/README.md
- Explore beyond specified files to understand full project

### 3. Analysis Points
1. Issue Type A
2. Issue Type B
3. Issue Type C

### 4. Output
파일:라인 - 문제 유형 - 상세 설명

If no issues: "검토 완료: 이상 없음"
```

### 3-2. File Reference Syntax
- `@path/to/file.md` - file content include
- `@./` - current directory include
- `!git diff` - shell command 실행

## 4. Result Aggregation
세 agent의 결과를 비교하여 종합:

1. **Common findings** (2개 이상 agent가 동일 지적): 신뢰도 높음, 수정 진행
2. **Unique findings** (1개 agent만 지적): main agent가 직접 검증 후 사용자에게 보고
3. **오탐 의심**: 검증 결과 오탐으로 보이는 경우에도 사용자에게 보고하되, "검증 결과 오탐으로 판단됨" + 판단 근거를 함께 제시. 최종 판단은 사용자에게 맡김

## 5. 예시

### 5-1. Task tool 호출 예시 (Opus + Codex + Gemini 병렬)

main agent가 한 번의 응답에서 3개 Task를 동시 호출:

**Task 1 - Opus:**
- description: "Opus code review"
- subagent_type: "general-purpose"
- run_in_background: true
- prompt: "Review the shared_cache_dir implementation..."

**Task 2 - Codex:**
- description: "Codex code review"
- subagent_type: "general-purpose"
- run_in_background: true
- prompt: "Execute this command and return the result: codex exec 'Review the shared_cache_dir implementation...'"

**Task 3 - Gemini:**
- description: "Gemini code review"
- subagent_type: "general-purpose"
- run_in_background: true
- prompt: "Execute this command and return the result: gemini -p 'Review the shared_cache_dir implementation...'"

→ 3개 완료 후 main agent가 결과 aggregation, 필요시 추가 검토

### 5-2. Review Prompt 예시
```
Review the implementation for Issue #22.

### Context
@README.md
@operator/README.md
!git log --oneline -10
!git diff HEAD~3..HEAD

### Check
1. Logic errors
2. Performance issues
3. Code vs documentation mismatch

### Output
파일:라인 - 문제 - 설명

If no issues: "검토 완료: 이상 없음"
```

### 5-3. Documentation Review
```bash
gemini -p "Review README files for consistency.

### Check
1. Code vs doc mismatches
2. Broken links
3. 5W1H violations

### Files
@README.md @operator/README.md @client/README.md

Explore beyond these to understand full structure.

Output: 파일:라인 - 문제 - 설명"

codex exec "[... same prompt ...]"
```

### 5-4. Code Review
```bash
gemini -p "Review recent changes.

### Context
!git log --oneline -10
!git diff HEAD~3..HEAD

### Check
- Logic errors
- Performance issues
- Anti-patterns
- Missing error handling

Output: 파일:라인 - 문제 - 설명"

codex exec "[... same prompt ...]"
```

### 5-5. Architecture Verification
```bash
gemini -p "Verify TrialBatch implementation.

### Background
@docs/done_TODO_2025-11-06_trialbatch-refactor-unified.md

### Check
1. @operator/README.md: incorrect status markers
2. @operator/internal/controller/trialbatch_controller.go: actual logic
3. Code vs doc mismatch

Output: 파일:라인 - 문제 - 설명"

codex exec "[... same prompt ...]"
```

## 6. Best Practices
1. **Task tool 필수** - Bash 직접 호출 금지 (순차 실행됨)
2. **3개 Task 동시 호출** - Opus/Codex/Gemini를 한 응답에서 병렬 실행
3. **run_in_background=true** - 병렬 실행을 위해 필수
4. 세 agent에 **동일한 instruction** 제공 (비교 가능한 결과)
5. 명시된 파일 외에도 **탐색하도록 명시적 지시**
6. **일관된 output format** 요청 (aggregation 용이)
7. 세 agent 모두 완료 대기 후 진행
8. **Result aggregation**: common findings는 수정 진행, unique findings는 검증 후 보고, 오탐 의심도 판단 근거와 함께 보고
9. TODO 문서 작성 시 agent findings를 background로 포함
10. **필요시 main agent 추가 검토** - 3개 결과 확인 후 심층 분석 필요하면 main agent가 직접 수행

## 7. 결과 대기 방법 (중요!)

**TaskOutput tool로 모든 task 완료 대기 → 1번만 취합**

### 7-1. TaskOutput 사용법
- `block=true`: task 완료까지 blocking 대기 (기본값)
- `block=false`: 즉시 현재 상태 반환 (non-blocking)
- `timeout`: 최대 대기 시간 (ms, 기본 30000, 최대 600000)

### 7-2. 올바른 대기 패턴

**3개 Task 실행 후, 3개 TaskOutput을 동시 호출:**

```
Step 1: Task 3개 동시 실행 (run_in_background=true)
├─ Task 1 (Opus)     → task_id_1 반환
├─ Task 2 (Codex)    → task_id_2 반환
└─ Task 3 (Gemini)   → task_id_3 반환

Step 2: TaskOutput 3개 동시 호출 (block=true)
├─ TaskOutput(task_id_1, block=true, timeout=600000)
├─ TaskOutput(task_id_2, block=true, timeout=600000)
└─ TaskOutput(task_id_3, block=true, timeout=600000)
→ 3개 모두 완료될 때까지 대기

Step 3: 결과 취합 (딱 1번만!)
→ 3개 결과를 한꺼번에 받아서 aggregation
```

### 7-3. 잘못된 대기 패턴 (금지!)

```
# 잘못된 예시 1: sleep 사용
Bash: sleep 60  ← 불필요한 대기, 비효율적

# 잘못된 예시 2: 순차적 TaskOutput
TaskOutput(task_id_1) → 취합  ← 중간 취합 금지!
TaskOutput(task_id_2) → 취합  ← 중간 취합 금지!
TaskOutput(task_id_3) → 취합  ← 3번이나 취합함

# 올바른 예시: TaskOutput 3개 동시 호출
TaskOutput(task_id_1) ─┐
TaskOutput(task_id_2) ─┼→ 모두 완료 후 1번만 취합
TaskOutput(task_id_3) ─┘
```

### 7-4. timeout 권장 설정
- 코드 리뷰: `timeout=600000` (10분)
- 간단한 분석: `timeout=300000` (5분)
- timeout 발생 시 해당 agent 결과 없이 나머지로 취합

## 8. 주의사항
- **Bash 직접 호출 절대 금지** - 순차 실행됨
- Task tool로 3개 subagent 생성 (Opus/Codex/Gemini)
- Codex/Gemini subagent는 내부에서 Bash로 CLI 호출
- **sleep 사용 금지** - TaskOutput의 block=true 사용
- **TaskOutput 3개 동시 호출** - 모든 agent 완료 대기
- **취합은 딱 1번만** - 3개 결과 수집 후 한꺼번에 aggregation
- 심층 분석 필요시 main agent가 추가 검토 수행

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/guny524) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
