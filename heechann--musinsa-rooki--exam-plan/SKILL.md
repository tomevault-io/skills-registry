---
name: exam-plan
description: Create a coding exam planning pack (Problem summary, Acceptance Criteria, Plan, Test Strategy) without writing code. Invoke before implementation. Use when this capability is needed.
metadata:
  author: heechann
---

<!-- ultrathink -->

# /exam-plan — 코딩 시험 Plan Pack 생성

## 목적
이 스킬은 **구현(코드 작성) 전에** 문제를 정확히 해석하고, 성공 기준과 검증 방법을 포함한 **재현 가능한 계획 문서**를 생성합니다.

- 지금은 **Plan only** 입니다.
- **코드 작성/패치/파일 수정/명령 실행 금지**
- 최종 출력은 아래 4개 파일에 **그대로 붙여넣을 수 있는 Markdown** 형태로 제공합니다.

## 입력 (읽을 파일)
- 기본: `SPECS/00_problem.md`
- 인자 제공 시: `$ARGUMENTS` 를 경로로 간주해 해당 파일을 먼저 읽습니다.
  - 예: `/exam-plan SPECS/00_problem.md`
  - 예: `/exam-plan docs/problem.md`

> 입력 파일이 비어 있거나 문제 원문이 없다면, 먼저 “문제 원문을 붙여넣어 달라”고 요청하고 멈춥니다.

## 출력 형식 (반드시 준수)
아래 4개 섹션을 **순서대로** 출력합니다. 각 섹션은 파일 단위로 완결되어야 합니다.

1) `SPECS/00_problem.md` (요약/입출력/제약/엣지/질문)  
2) `SPECS/02_acceptance.md` (AC Must/Should/Could + 평가 기록 템플릿)  
3) `SPECS/01_plan.md` (3시간 타임박스 5~10단계 + 단계별 검증)  
4) `SPECS/03_test_strategy.md` (테스트 우선순위 + AC↔테스트 매핑)

각 섹션은 아래 형태로 출력합니다:

- `### FILE: <path>`
- 바로 다음 줄부터 파일 내용을 **하나의 fenced code block**(```md)으로 감싸서 제공합니다.

예시:
### FILE: SPECS/01_plan.md
```md
(여기에 파일 전체 내용)
```

## 작성 규칙 (품질 기준)
### 문제 해석
- “내 말로 5줄 요약”을 가장 먼저 작성합니다.
- 입력/출력을 **정확히 정의**합니다(공백/줄바꿈/포맷 포함).
- 제약조건으로 시간복잡도/공간복잡도의 현실적 상한을 한 줄로 명시합니다.

### Acceptance Criteria (AC)
- Must/Should/Could로 나눕니다.
- Must는 최소 5개 작성합니다.
- 모든 Must는 **테스트 가능**한 문장이어야 합니다(정성 표현 금지).

### 계획(Plan)
- 5~10단계로 쪼개고, 각 단계에:
  - 산출물(무엇이 만들어지는지)
  - 검증(어떤 테스트/커맨드로 확인하는지)
- “7분 룰” 비상 프로토콜을 포함합니다.
- 구현 시작 전 멈추도록 마지막에 “내 승인 대기”를 반드시 포함합니다.

### 테스트 전략(Test Strategy)
- 단위→통합→엣지 순서로 제시합니다.
- Must AC 각각에 대해 “매핑되는 테스트 이름”을 제안합니다.
- 가장 위험한 로직 3개를 선정하고, 이를 깨뜨릴 테스트를 우선 제안합니다.

## 금지 사항
- 코드 작성/코드 블록(Java 등) 생성 금지 (문서 템플릿만)
- 명령 실행 지시(예: `./gradlew test`)는 “검증 방법” 섹션에 텍스트로 언급만 가능
- 구현/리팩터링/패치 제안 금지 (Plan only)

## 종료 조건
4개 파일 출력 후, 마지막 줄에 다음 문구를 출력하고 종료합니다:

**STOP: 내 승인 대기 (Plan Mode 완료)**

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/heechann) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
