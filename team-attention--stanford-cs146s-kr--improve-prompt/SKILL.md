---
name: improve-prompt
description: WebSearch 팩트체크 스킵 Use when this capability is needed.
metadata:
  author: team-attention
---

# improve-prompt Skill

나노바나나 프롬프트의 정확성을 검증하고 개선합니다.

## 사용법

```bash
# 기본 사용 - 단일 챕터
/improve-prompt week1/deep-dive-llms/tokenization

# 단일 페이지 콘텐츠
/improve-prompt week1/how-openai-uses-codex

# 챕터 전체 검증
/improve-prompt week1/deep-dive-llms --all-chapters

# 옵션
/improve-prompt week1/slug --skip-apply      # 리포트만 생성
/improve-prompt week1/slug --auto-apply      # 자동 적용
/improve-prompt week1/slug --verify          # 수정 후 재검증
/improve-prompt week1/slug --skip-factcheck  # 팩트체크 스킵
```

## 검증 기준

### 한글 번역본 대비 검증

| 검증 항목 | 설명 |
|----------|------|
| 개념 정확성 | 핵심 개념이 한글 번역본과 일치하는지 |
| 수치 정확성 | 숫자, 비율, 통계가 정확한지 |
| 용어 일관성 | 영어 용어가 올바르게 사용되었는지 |
| 구조 완전성 | Mermaid/표 등이 원문 내용을 반영하는지 |
| 문맥 정합성 | 설명이 문맥상 올바른지 |

### WebSearch 팩트체크

| 검증 항목 | 검색 예시 |
|----------|----------|
| 기술 용어 정의 | "BPE Byte Pair Encoding algorithm" |
| 수치/통계 확인 | "GPT-4 vocabulary size tokens" |
| 인물/조직 정보 | "Andrej Karpathy Tesla OpenAI" |
| 날짜/버전 정보 | "GPT-4 release date" |

## 입출력

### 입력 파일

| 소스 | 경로 |
|------|------|
| 나노바나나 프롬프트 | `.claude/outputs/nanobanana/week{N}/{slug}-cheatsheet-prompt.md` |
| 한글 번역본 | `docs/week{N}/{slug}/kr/index.md` |

### 챕터 구조 콘텐츠

| 소스 | 경로 |
|------|------|
| 나노바나나 프롬프트 | `.claude/outputs/nanobanana/week{N}/{parent}/{child}-cheatsheet-prompt.md` |
| 한글 번역본 | `docs/week{N}/{parent}/kr/{child}.md` |

### 출력 파일

```
.claude/outputs/improve-prompt/
└── week{N}/{slug}/
    └── evaluation-report.json
```

## 워크플로우

```
/improve-prompt week1/deep-dive-llms/tokenization
                │
                ▼
┌───────────────────────────────────────────────┐
│ 1. 경로 파싱 및 파일 로드                       │
│    - 나노바나나 프롬프트 파일 읽기               │
│    - 한글 번역본 파일 읽기                      │
└───────────────────────────────────────────────┘
                │
                ▼
┌───────────────────────────────────────────────┐
│ 2. Task: prompt-evaluator                     │
│    - 프롬프트 ↔ 한글 번역본 비교               │
│    - 핵심 개념/수치 추출                        │
│    - 불일치/누락/오류 탐지                      │
│    → JSON 이슈 리포트                          │
└───────────────────────────────────────────────┘
                │
                ▼
┌───────────────────────────────────────────────┐
│ 3. Task: fact-checker (--skip-factcheck 시 생략)│
│    - 추출된 기술 용어/수치 WebSearch 검증       │
│    - 검색 결과와 claim 비교                     │
│    → 팩트체크 결과 이슈에 추가                  │
└───────────────────────────────────────────────┘
                │
                ▼
┌───────────────────────────────────────────────┐
│ 4. 리포트 저장                                 │
│    evaluation-report.json                     │
└───────────────────────────────────────────────┘
                │
                ▼
┌───────────────────────────────────────────────┐
│ 5. 이슈별 사용자 컨펌 (--skip-apply 시 생략)   │
│    AskUserQuestion으로 이슈별 승인/거부         │
└───────────────────────────────────────────────┘
                │
                ▼
┌───────────────────────────────────────────────┐
│ 6. 승인된 수정사항 적용                        │
│    Edit tool로 프롬프트 파일 수정              │
└───────────────────────────────────────────────┘
                │
                ▼
┌───────────────────────────────────────────────┐
│ 7. (선택) 재검증 (--verify 시)                 │
│    Step 2부터 재실행                           │
└───────────────────────────────────────────────┘
```

## 실행 지침

이 스킬이 호출되면 다음 단계를 따르세요.

### Step 1: 경로 파싱 및 파일 경로 결정

```
입력: week1/deep-dive-llms/tokenization
→ weekNum: 1
→ parent: deep-dive-llms
→ child: tokenization

프롬프트 경로:
.claude/outputs/nanobanana/week1/deep-dive-llms/tokenization-cheatsheet-prompt.md

한글 번역본 경로:
docs/week1/deep-dive-llms/kr/tokenization.md
```

단일 페이지 콘텐츠 (child가 없는 경우):
```
입력: week1/how-openai-uses-codex
→ weekNum: 1
→ slug: how-openai-uses-codex

프롬프트 경로:
.claude/outputs/nanobanana/week1/how-openai-uses-codex-cheatsheet-prompt.md

한글 번역본 경로:
docs/week1/how-openai-uses-codex/kr/index.md
```

### Step 2: 파일 로드

```
Read tool로 두 파일 읽기:
1. 나노바나나 프롬프트 파일
2. 한글 번역본 파일

파일이 없는 경우:
- 프롬프트 없음: "❌ 프롬프트 파일을 찾을 수 없습니다. /nanobanana 먼저 실행해주세요."
- 번역본 없음: "❌ 한글 번역본을 찾을 수 없습니다. /translate-reading 먼저 실행해주세요."
```

### Step 3: prompt-evaluator 에이전트 호출

```
Task tool 호출:
- subagent_type: "general-purpose"
- description: "prompt-evaluator - {slug} 검증"
- prompt:
  ```
  아래 에이전트 지침을 따라 프롬프트를 검증하세요.

  ## 에이전트 지침
  [.claude/agents/improve-prompt/prompt-evaluator.md 내용]

  ## 나노바나나 프롬프트
  [프롬프트 파일 내용]

  ## 한글 번역본
  [한글 번역본 파일 내용]

  JSON 형식으로 결과를 출력하세요.
  ```

결과: JSON (extractedClaims, issues)
JSON 블록(```json ... ```) 추출하여 파싱
```

### Step 4: fact-checker 에이전트 호출 (--skip-factcheck가 아닌 경우)

```
Step 3에서 추출된 claims 중 팩트체크가 필요한 항목 선별:
- type이 "number", "statistic", "person", "organization", "date" 인 것들

Task tool 호출:
- subagent_type: "general-purpose"
- description: "fact-checker - {slug} 팩트체크"
- prompt:
  ```
  아래 에이전트 지침을 따라 팩트체크를 수행하세요.

  ## 에이전트 지침
  [.claude/agents/improve-prompt/fact-checker.md 내용]

  ## 검증할 Claims
  [extractedClaims 목록 JSON]

  WebSearch tool을 사용하여 각 claim을 검증하고 JSON 형식으로 결과를 출력하세요.
  ```

결과: JSON (factCheckResults)
팩트체크 실패 항목을 Step 3의 issues에 추가
```

### Step 5: 리포트 저장

```
출력 디렉토리 생성:
mkdir -p .claude/outputs/improve-prompt/week{N}/{parent}/

결과 저장:
Write tool로 evaluation-report.json 저장

--skip-apply 옵션인 경우:
→ 리포트 내용 출력 후 종료
→ Step 6, 7 스킵
```

### Step 6: 이슈별 사용자 컨펌 (--skip-apply가 아닌 경우)

```
이슈를 severity 순서로 정렬: critical → major → minor

각 이슈에 대해:

--auto-apply 옵션인 경우:
→ 모든 이슈 자동 승인

그 외:
→ AskUserQuestion tool 사용

AskUserQuestion 호출 예시:
questions:
  - question: "이 수정사항을 적용하시겠습니까?"
    header: "[{severity}] {type}"
    options:
      - label: "적용"
        description: |
          위치: {location}
          현재: "{current}"
          수정: "{suggested}"
          이유: {reason}
      - label: "건너뛰기"
        description: "이 이슈를 무시하고 다음으로 진행"
    multiSelect: false

사용자 선택 처리:
- "적용": approvedIssues 배열에 추가
- "건너뛰기": skippedIssues 배열에 추가
- "Other" (직접 입력): customFixes 배열에 추가
```

### Step 7: 승인된 수정사항 적용

```
approvedIssues와 customFixes의 각 항목에 대해:

1. Read tool로 프롬프트 파일 읽기
2. 수정할 위치 찾기 (location 정보 활용)
3. Edit tool로 수정 적용:
   - old_string: current 값
   - new_string: suggested 또는 custom 값

주의:
- 한글 포함 시 정확한 문자열 매칭 필요
- 줄바꿈, 공백 등 정확히 맞춰야 함
```

### Step 8: (선택) 재검증 (--verify 옵션인 경우)

```
--verify 옵션이 있으면:
→ "🔄 수정사항 적용 완료. 재검증을 시작합니다..."
→ Step 3부터 다시 실행
→ 새 이슈가 없으면 완료
```

### Step 9: 결과 요약 출력

```
완료 메시지 형식:

✅ 프롬프트 검증 완료!

📊 **검증 결과**
- 검증된 프롬프트: {path}
- 추출된 Claims: {N}개
- 팩트체크: {N}건 (성공 {N}, 실패 {N})
- 발견된 이슈: {N}건
  - Critical: {N}건
  - Major: {N}건
  - Minor: {N}건

📝 **적용된 수정** (--skip-apply가 아닌 경우)
- 승인됨: {N}건
- 건너뜀: {N}건

📁 **리포트 저장 위치**
.claude/outputs/improve-prompt/week{N}/{slug}/evaluation-report.json

💡 **다음 단계**
수정된 프롬프트를 나노바나나 프로에 붙여넣어 새 치트시트를 생성하세요.
```

---

## --all-chapters 모드

`--all-chapters` 옵션이 있으면 부모 콘텐츠의 모든 챕터를 순차 검증합니다.

### Step 0: 챕터 목록 수집

```
부모 디렉토리 확인:
.claude/outputs/nanobanana/week{N}/{parent}/

Glob으로 챕터 프롬프트 파일 목록 수집:
*.cheatsheet-prompt.md 패턴

각 챕터에 대해 Step 1~9 실행
```

---

## 이슈 유형

| 유형 | 설명 | 심각도 |
|------|------|--------|
| `factual_error` | 팩트체크 실패, 틀린 정보 | critical |
| `concept_mismatch` | 핵심 개념이 원문과 불일치 | critical |
| `number_error` | 수치/통계 오류 | major |
| `missing_content` | 중요 내용 누락 | major |
| `terminology_error` | 용어 오용/불일치 | minor |
| `context_error` | 문맥상 부적절한 표현 | minor |

## 심각도

| 레벨 | 기준 |
|------|------|
| `critical` | 핵심 내용 오류, 팩트체크 실패 |
| `major` | 중요 정보 누락/왜곡 |
| `minor` | 표현 개선, 일관성 문제 |

## 에러 처리

| 상황 | 처리 |
|------|------|
| 프롬프트 없음 | "❌ 프롬프트 파일을 찾을 수 없습니다. /nanobanana 먼저 실행해주세요." |
| 번역본 없음 | "❌ 한글 번역본을 찾을 수 없습니다. /translate-reading 먼저 실행해주세요." |
| JSON 파싱 실패 | 원본 출력 저장 후 수동 검토 안내 |
| WebSearch 실패 | 해당 claim 스킵, 경고 메시지 출력 |

## Agent 파일

- `.claude/agents/improve-prompt/prompt-evaluator.md` - 프롬프트 평가 에이전트
- `.claude/agents/improve-prompt/fact-checker.md` - WebSearch 팩트체크 에이전트

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/team-attention) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
