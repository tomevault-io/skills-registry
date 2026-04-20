---
name: i18n-copy-manager
description: UI/앱/웹의 다국어(i18n/l10n) 문구를 작성·번역·검수·정비하고, 언어별 톤앤매너(Voice)를 일관되게 유지하며, 용어집(Glossary)·스타일 가이드·locale(JSON/ARB) 품질을 관리한다. “번역해줘”, “영문/일본어 문구”, “마이크로카피 다듬기”, “톤 맞춰줘(같은 언어에서 일관되게)”, “i18n 키/locale 파일 정리/검수”, “placeholder/ICU 메시지 확인” 같은 요청에 사용한다. Use when this capability is needed.
metadata:
  author: huved
---

# i18n-copy-manager

## 목표

- 언어별로 “한 사람이 말하는 것처럼” 느껴지도록 톤앤매너를 일관되게 유지한다(특히 같은 언어 내 화면 간 일관성).
- 기능/프로젝트/화면 문맥에 맞는 번역·로컬라이징·카피라이팅을 제공하고, 필요하면 개선까지 수행한다.

## 핵심 원칙 (반드시 준수)

- **컨텍스트 우선**: 문구는 UI 요소 타입(버튼/타이틀/에러/토스트 등)과 사용 흐름이 핵심이다. 키/문구만 던져주면, 코드/디자인 문맥을 먼저 찾거나 최소 질문으로 보완한다.
- **언어별 Voice 고정**: 언어마다 존대/격식/말투를 하나로 고정하고 섞지 않는다(예: 일본어는 `です/ます` vs `だ/である`, 한국어는 `해요체` vs `합니다체`).
- **용어 일관성**: 핵심 도메인 용어(기능명/설정명/역할명)는 Glossary로 고정 번역을 관리한다.
- **기술 제약 보존**: placeholder/ICU/변수/마크업은 의미를 유지한 채 그대로 보존한다(문자 치환 규칙 포함).
- **UX 마이크로카피 품질**: 짧고 행동 중심, 중복/모호함 최소화, 에러는 blame 없이 “다음 행동(재시도/확인)”을 제공한다.

## 빠른 시작 (워크플로 의사결정 트리)

아래 중 무엇인지 먼저 판별하고 해당 절차로 진행한다.

- A) **새 문구 작성(멀티 언어)** → `문맥 수집` → `Voice/Glossary 확인` → `초안 작성` → `QA` → `출력/반영`
- B) **번역/로컬라이징** → `문맥 수집` → `의미 해석` → `언어별 자연스러운 UI 카피로 재작성` → `Voice/용어 통일` → `QA`
- C) **기존 번역 검수/개선** → `문맥 확인(코드/화면)` → `문제점(P0/P1) 분류` → `개선안 제시` → `일괄 톤 정렬`
- D) **locale 파일 정리/품질 점검** → `scripts/i18n_audit.py` 실행 → `누락/불일치/placeholder 문제 수정`

## 1) 문맥 수집 (질문 최소화)

- 가능한 한 **질문 0~3개**로 시작하고, 답이 없으면 합리적 가정을 명시하고 진행한다.
- 최소 필요 정보(없으면 추정):
  - 대상 로케일(예: `ko`, `en`, `ja-JP`)
  - UI 요소 타입(버튼/타이틀/설명/에러/토스트/빈 상태/알림)
  - 화면/플로우 목적(온보딩/로그인/결제/설정 등) + 사용자가 하려는 행동
  - 기술 제약(placeholder/ICU, 글자 수 제한, 줄바꿈, 대문자 규칙)
- 코드가 있으면 키/문구 사용 위치를 먼저 찾는다(예: `rg "<key>" .`).

## 2) Voice & Glossary 설정/적용

- 이미 해당 언어 UI 문구가 존재하면, 거기서 **Voice를 추출**해 동일 톤으로 맞춘다(“화면마다 다른 사람이 말하는 느낌” 제거).
- 기준이 없으면 `references/voice-consistency.md`를 참고해 언어별 Voice Profile을 먼저 만든 뒤 진행한다.
- 도메인 용어는 Glossary로 고정한다(예: 기능명/탭명/권한명/멤버 역할명).
  - 필요하면 `assets/glossary.template.md`를 복사해 프로젝트에 두고 관리한다.

## 3) 작성/번역/검수 규칙 (UI 카피 중심)

- **버튼/CTA**: 짧고 행동 중심(동사/행동), 동일 기능은 동일 동사로 통일.
- **타이틀**: 상태/행동을 명확히, 너무 긴 문장 지양.
- **설명/헬프 텍스트**: “왜/무엇을/어떻게” 중 필요한 것만. 한 문장씩 분리.
- **에러 문구**: 원인 추정 단정 금지, 사용자 탓 표현 금지, 다음 행동(재시도/확인/문의)을 제공.
- **빈 상태**: 현재 상태 설명 + 첫 행동(CTA) 조합.

## 4) 기술/형식 QA (placeholder/ICU 포함)

- placeholder/변수/마크업이 있는 문구는 **형태를 보존**한다(이름/개수/중괄호/이중중괄호/printf).
- ICU(MessageFormat) 패턴(복수형/성별/선택)은 구조를 유지한 채 문장만 자연스럽게 만든다.
- 길이 제약이 있으면, 언어별로 과도한 확장(+30~50%)을 피하고 대안을 제시한다.

## 출력 포맷 (권장)

- 문구/키 단위로 아래 정보를 표로 제공한다:
  - `key(있다면)` / `UI 요소 타입` / `base(원문)` / `target(번역/개선문)` / `비고(근거/주의점: Voice/용어/placeholder)`
- 대량 수정이면 “공통 변경 규칙(Voice/용어)”을 먼저 제시하고, 예외만 비고로 남긴다.

## 리소스

### scripts/
- `scripts/i18n_audit.py`: base/target locale(JSON/ARB) 간 키 누락/여분/placeholder 불일치/미번역(동일 문자열) 등을 리포트한다.

예시:

    python3 "$CODEX_HOME/skills/i18n-copy-manager/scripts/i18n_audit.py" \\
      --base ./l10n/en.arb \\
      --targets ./l10n/ja.arb ./l10n/ko.arb \\
      --format md \\
      --fail-on-issues

### references/
- `references/voice-consistency.md`: 언어별 Voice Profile 설계/점검 가이드(특히 같은 언어 내 톤 일관성).
- `references/translation-qa.md`: 번역/카피 QA 체크리스트(문맥/용어/톤/기술 제약/접근성).

### assets/
- `assets/copy-brief.template.md`: 문구 요청/번역 요청 시 컨텍스트를 빠르게 채우는 템플릿.
- `assets/voice-profile.template.md`: 언어별 Voice Profile 템플릿.
- `assets/glossary.template.md`: 프로젝트 Glossary 템플릿(언어 열은 필요에 맞게 추가/삭제).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/huved) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
