---
name: workflow-validate
description: Validate technical feasibility with prototypes or tests Use when this capability is needed.
metadata:
  author: specvital
---

# Technical Validation Command

## User Input (Optional)

```text
$ARGUMENTS
```

**Optional input**:

- **Empty**: Auto-determine validation method based on analysis.md
- **Provided**: Must consider user instructions (e.g., specific validation method, additional requirements)

---

## Outline

1. **Check Prerequisites**:
   - Verify `docs/work/WORK-{name}/analysis.md` exists
   - If not, ERROR: "Run /workflow-analyze first"

2. **Load Analysis Document**:
   - Extract selected approach from analysis.md
   - Identify key technical assumptions and risks

3. **Determine Validation Method**:
   - Choose appropriate validation approach based on context:
     - Prototype implementation
     - UI/UX verification (Playwright MCP)
     - TDD approach
     - Library/API exploration
     - Technical documentation research
     - User verification delegation

4. **Execute Validation**:
   - Perform selected validation method
   - Create prototype code in `__prototype__/{feature-name}/` if needed
   - Collect results and evidence

5. **Assess Results**:
   - Determine status: ✅ Success / ⚠️ Partial Success / ❌ Failure
   - Document findings and constraints

6. **Write Documents** (Dual Language):
   - Create `docs/work/WORK-{name}/validation.ko.md` (Korean - for user reference)
   - Create `docs/work/WORK-{name}/validation.md` (English - for agent consumption)
   - Include clear next steps recommendation

---

## Key Rules

### 📝 Documentation Language

**CRITICAL**: You must generate **TWO versions** of all documents:

1. **Korean version** (`validation.ko.md`): For user reference - written in Korean
2. **English version** (`validation.md`): For agent consumption - written in English

**Both versions must contain identical structure and information**, only the language differs.

### 🎯 Validation Principles

1. **Focus on Core Risks**: Validate only critical technical uncertainties
2. **Practical Approach**: Sufficient confidence over perfect validation
3. **Clear Judgement**: Explicit success/failure with next steps

### ✅ Must Do

- Validate **only** what's uncertain
- Create minimal working code (if prototype)
- Document all findings clearly
- Provide actionable next steps
- Reference analysis.md for context
- Store prototype in `__prototype__/` directory

### ❌ Must Not Do

- Full implementation (save for execute phase)
- Validate obvious/known facts
- Repeat analysis.md content
- Give ambiguous conclusions ("maybe works")
- Over-engineer the validation

### 🔬 Validation Methods Selection

**Choose based on uncertainty type**:

| Uncertainty Type       | Validation Method        | Output                      |
| ---------------------- | ------------------------ | --------------------------- |
| Core logic feasibility | Prototype implementation | Working code + results      |
| UI/UX changes          | Playwright verification  | Screenshots + test results  |
| Complex algorithms     | TDD approach             | Test code + edge cases      |
| External dependencies  | Library exploration      | Sample code + compatibility |
| Standards/patterns     | Documentation research   | Summary + recommendations   |
| Environment-specific   | User delegation          | Test guide + checklist      |

### 📁 Prototype Code Management

**Location**: `__prototype__/{feature-name}/`

**How to create**:

- Validate directly in actual codebase (modifying files is OK)
- After validation, extract core logic to `__prototype__/`
- Don't commit actual code changes (revert or leave as-is)

**Purpose**:

- Reference for plan/execute phases
- Archive of validated core logic
- Proof of technical feasibility

**Lifecycle**:

- Created during validate
- Referenced in execute
- User manages cleanup

---

## Document Template

Files to create:

- `docs/work/WORK-{task-name}/validation.ko.md` (Korean version)
- `docs/work/WORK-{task-name}/validation.md` (English version)

### Template A: AI-Completed Validation

```markdown
# 기술 검증 보고서

## 🎯 검증 목표

**핵심 질문**: [검증하려는 기술적 불확실성]
**검증 범위**: [검증 대상 및 제외 사항]

---

## 🔬 검증 방법

**선택한 방법**: [프로토타입/테스트/조사 등]
**검증 환경**: [사용한 도구 및 설정]

---

## 📊 검증 결과

**상태**: ✅ 성공 / ⚠️ 부분 성공 / ❌ 실패

**핵심 발견사항**:

- [주요 발견 1]
- [주요 발견 2]

---

## 💡 발견된 사항

### 긍정적 요소

- [예상대로 작동하는 부분]
- [추가로 발견한 장점]

### 제약사항/리스크

- [기술적 제약]
- [성능 이슈]
- [호환성 문제]

---

## 🛠️ 프로토타입 코드 (if created)

**위치**: `__prototype__/{feature-name}/`

**핵심 구현**:

- [구현한 핵심 로직 설명]
- [테스트 방법 및 결과]

**참고사항**:

- [execute 단계에서 주의할 점]
- [개선 가능한 부분]

---

## ✅ 권장사항

**다음 단계**: `/workflow-plan` 진행 / `/workflow-analyze` 재검토

**구현시 주의사항**:

- [주의사항 1]
- [주의사항 2]

**대안 (필요시)**:

- [대안 1]: [장단점]
- [대안 2]: [장단점]

---

## 📚 참고자료

- [관련 문서 링크]
- [참고한 라이브러리 문서]
```

### Template B: User Verification Request

```markdown
# 기술 검증 가이드

## 🎯 검증 배경

**AI가 직접 검증할 수 없는 이유**: [환경/접근/도메인 제약]
**사용자 검증이 필요한 부분**: [구체적 검증 대상]

---

## ✅ 검증 체크리스트

### 필수 확인 사항

- [ ] [확인 항목 1]: [확인 방법]
- [ ] [확인 항목 2]: [확인 방법]
- [ ] [확인 항목 3]: [확인 방법]

---

## 🧪 테스트 시나리오

### 시나리오 1: [제목]

**목적**: [무엇을 확인하려는지]

**단계**:

1. [구체적 실행 단계]
2. [구체적 실행 단계]
3. [구체적 실행 단계]

**예상 결과**: [정상 동작시 기대 결과]

**확인 포인트**:

- [무엇을 봐야 하는지]
- [어떤 값을 확인해야 하는지]

### 시나리오 2: [제목]

[시나리오 1과 동일한 구조]

---

## ❓ 확인 질문

1. **[구체적 질문]**
   - 배경: [왜 이것이 중요한지]
   - 판단 기준: [어떻게 판단할지]

2. **[구체적 질문]**
   - 배경: [왜 이것이 중요한지]
   - 판단 기준: [어떻게 판단할지]

---

## 📋 검증 완료 후

**결과 공유 방법**:

- 각 체크리스트 항목별 통과/실패 표시
- 발견된 문제나 제약사항 설명
- 스크린샷이나 로그 첨부 (필요시)

**다음 단계**:

- 검증 성공시: `/workflow-plan` 진행
- 문제 발견시: 대안 논의 필요
```

---

## 📊 Validation Status Guidelines

### ✅ Success Criteria

- All core technical assumptions validated
- No blocking issues found
- Clear path to implementation
- **Next**: Proceed to `/workflow-plan`

### ⚠️ Partial Success Criteria

- Main approach works with constraints
- Workarounds or alternatives available
- Trade-offs acceptable
- **Next**: Document constraints, get user confirmation

### ❌ Failure Criteria

- Core approach not feasible
- Blocking issues without workarounds
- Fundamental assumptions invalid
- **Next**: Return to `/workflow-analyze` for re-evaluation

---

## 🎯 Spike Principles

This validation follows **Agile Spike** methodology:

1. **Risk Reduction**: Focus on highest risk items
2. **Just Enough**: Minimal code for maximum learning
3. **Throwaway Code**: Prototypes are for learning, not production
4. **Clear Outcome**: Binary decision on feasibility

---

## Execution

Now start the validation task according to the guidelines above.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/specvital) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
