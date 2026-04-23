---
name: adr
description: 아키텍처 결정 기록(ADR)을 docs/decisions/에 생성 또는 갱신. PRICE 기준(정책, 가역성, 영향, 제약, 예외)에 해당하는 결정 기록, 분석 단계(workflow-analyze, epic-analyze) 권고, 또는 임의 대화 중 수동 호출 시 사용. Use when this capability is needed.
metadata:
  author: kubrickcode
---

# ADR 생성 명령

## 사용자 입력

```text
$ARGUMENTS
```

사용자 입력이 비어있지 않다면 **반드시** 고려해야 합니다.

---

## 개요

1. **사용자 입력 파싱**:
   - $ARGUMENTS에서 결정 주제 추출
   - ADR 슬러그 생성 (2-4 단어, 영문, 하이픈 구분)

2. **맥락 수집**:
   - 대화 이력과 사용자가 태그한 파일을 맥락으로 활용
   - analysis.md가 태그되었으면: 결정, 선택지, 근거 추출 (증류, 복제 아님)

3. **기존 ADR 스캔**:
   - `docs/decisions/` 파일명 목록으로 다음 번호 결정 및 슬러그 기반 중복 후보 식별
   - 후보 파일만 읽어 `status`, `name`, `description` 확인 후 대체 여부 판단

4. **ADR 문서 작성**:
   - `docs/decisions/NNNN-slug.md` 생성 (한글)
   - analysis.md 기반이면: 증류(distill) — 결정과 근거만 추출, 전체 복사 금지
   - 단독 호출이면: 대화 맥락에서 작성

5. **상호 링크** (해당 시):
   - 기존 ADR 대체 시: 이전 ADR 상태 갱신

6. **보고**:
   - 생성된 파일 확인
   - 트리거된 PRICE 기준 표시

---

## 핵심 규칙

### 📝 문서 작성 언어

**중요**: ADR 문서는 **한글**로 작성해야 합니다.
기술 용어는 영문을 괄호 안에 포함할 수 있습니다.

### 📝 증류, 복제 아님

소스 자료(분석 문서, 대화)가 있을 때:

- 소스 = 상세한 분석이나 논의 (작업 범위)
- ADR = 증류된 결정 기록 (0.5-1 페이지, 프로젝트 범위)
- 추출: 무엇을 결정했는가, 왜, 무엇을 포기했는가
- 소스 전체를 ADR에 복사하지 말 것

### 📝 번호 체계

- 0-패딩 4자리: `0001`, `0002`, ...
- `docs/decisions/`에서 가장 높은 기존 번호 스캔
- 디렉토리 미존재 시 생성 후 `0001`부터 시작

### 📝 상태 값

- `수락됨` (Accepted) — 새 ADR 기본값
- `대체됨` (Superseded) — `supersedes` 필드와 함께 사용
- `폐기됨` (Deprecated)

---

## ✅ 해야 할 것

- 구체적 맥락 포함 (추상적 설명 금지)
- 검토한 모든 선택지와 장단점 나열
- 수용한 트레이드오프 명시
- 버전 관리되는 출처 링크 (PR, 커밋, 다른 ADR) 가능 시 포함

## ❌ 하지 말아야 할 것

- 장황한 설명 — 1페이지 이내
- 소스 자료 통째로 복제
- 트레이드오프 생략 또는 단점 없는 척
- 사소한 결정에 ADR 생성 (`rules/adr.md` 제외 목록 참조)

---

## 문서 템플릿

생성할 파일: `docs/decisions/NNNN-slug.md` (한글)

```markdown
---
name: [ADR 제목]
description: [결정 사항 1줄 요약]
status: 수락됨
date: YYYY-MM-DD
supersedes: null
---

# ADR-NNNN: [제목]

## 맥락

[이 결정이 필요한 상황. 구체적 시나리오 포함.]

## 결정 요인

- [핵심 요인 1]
- [핵심 요인 2]

## 검토한 선택지

### 선택지 1: [이름]

- 장점: ...
- 단점: ...

### 선택지 2: [이름]

- 장점: ...
- 단점: ...

## 결정

[선택한 접근법]. [핵심 근거 1-2문장.]

## 결과

- 긍정적: ...
- 수용한 트레이드오프: ...

## 관련 문서

- [버전 관리되는 출처 — PR, 커밋, 다른 ADR 등]
```

---

## 실행

이제 위 지침에 따라 작업을 시작하세요.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kubrickcode) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
