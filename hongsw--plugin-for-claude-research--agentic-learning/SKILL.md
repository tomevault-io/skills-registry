---
name: agentic-learning
description: Claude Code 자기주도 학습 스킬. "/agentic-learning", "/learn", "학습", "스킬 배우기" 요청에 사용. ai-native-camp/camp-1 기반의 인터랙티브 학습 프레임워크. PPTX 자동 생성 프로젝트를 통일 예시로 사용. Use when this capability is needed.
metadata:
  author: hongsw
---

# Self-Learning Skill — Claude Code 자기주도 학습

## Core Purpose

사용자가 Claude Code의 핵심 기능을 **스스로 학습**할 수 있도록 돕는 인터랙티브 학습 프레임워크입니다.
[ai-native-camp/camp-1](https://github.com/ai-native-camp/camp-1) 커리큘럼을 기반으로 설계되었습니다.

### 통일 주제: PPTX 자동 생성 프로젝트

모든 레볼루션은 하나의 프로젝트를 공통 예시로 사용합니다.

```
목표: Claude Code로 PPTX를 자동으로 만드는 팀 시스템 구축

담당 역할 3가지:
  🎨 테마 설정 담당  — 슬라이드 디자인, 색상, 폰트 결정
  ✍️  내용 수정 담당  — 슬라이드 텍스트, 구조, 논리 흐름
  🖨️  렌더링 담당    — HTML → PPTX 변환, 파일 출력
```

각 레볼루션마다 이 프로젝트에 새로운 Claude Code 기능을 적용하며 점진적으로 완성합니다.

---

### 학습 방식: STOP 프로토콜

각 레볼루션은 반드시 **2턴 구조**를 따릅니다:

**Phase A (1턴)**: 개념 설명 → 실습 지시 → **STOP** (퀴즈 없음, 질문 없음)
- 참조 문서의 EXPLAIN 섹션을 읽고 설명
- 참조 문서의 EXECUTE 섹션을 읽고 실습 안내 (PPTX 프로젝트 맥락 포함)
- 마무리: "👆 위 내용을 직접 실행해보세요. 실행이 끝나면 '완료' 또는 '다음'이라고 입력해주세요."

**Phase B (2턴)**: 퀴즈 → 피드백 → 다음 레볼루션 안내
- 참조 문서의 QUIZ 섹션을 읽고 AskUserQuestion으로 퀴즈 출제
- 정답/오답 피드백 제공
- 다음 레볼루션으로 이동 여부 확인

### 절대 규칙
1. Phase A에서 절대 AskUserQuestion을 호출하지 않는다
2. Phase A에서 퀴즈 내용을 절대 노출하지 않는다
3. "해보셨나요?" 같은 질문을 하지 않는다
4. 각 레볼루션 시작 전 공식 문서 URL을 출력한다
5. 개념 설명 후 반드시 PPTX 프로젝트에서의 활용 예시를 보여준다

---

## 학습 과정 (Learning Pipeline)

### 레볼루션 0: 환경 설정
**Prompt**: `prompts/setup.md`
**Purpose**: Claude Code 설치 및 초기 설정
**PPTX 연결**: PPTX 프로젝트 디렉토리 구조 만들기
**Reference**: `references/rev0-setup.md`

### 레볼루션 1: 체험 — 먼저 느껴보기
**Prompt**: `prompts/experience.md`
**Purpose**: Claude Code의 가능성을 3가지 데모로 체험
**PPTX 연결**: "PPTX 만들어줘" 한 마디로 슬라이드 생성 체험
**Reference**: `references/rev1-experience.md`

### 레볼루션 2: 왜 터미널인가?
**Prompt**: `prompts/why-cli.md`
**Purpose**: CLI 기반 Claude Code의 필요성 이해
**PPTX 연결**: PPTX 반복 생성 자동화 vs 수작업 비교
**Reference**: `references/rev2-why.md`

### 레볼루션 3: 7대 핵심 기능
**Prompt**: `prompts/core-features.md`
**Purpose**: Claude Code 7대 핵심 기능 학습
**PPTX 연결**: 각 기능을 PPTX 프로젝트에 직접 적용
**References**:
- `references/rev3-1-claude-md.md` — CLAUDE.md → PPTX 스타일 가이드 정의
- `references/rev3-2-skill.md`    — Skill → pptx-theme-setter 스킬 제작
- `references/rev3-3-mcp.md`      — MCP → Playwright로 PPTX 렌더링
- `references/rev3-4-subagent.md` — Subagent → 테마/내용/렌더링 독립 작업
- `references/rev3-5-agent-teams.md` — Agent Teams → 3담당 협업 시스템
- `references/rev3-6-hook.md`     — Hook → 저장 시 자동 품질 검사
- `references/rev3-7-plugin.md`   — Plugin → PPTX 도구 패키지 배포

### 레볼루션 4: 기초 다지기
**Prompt**: `prompts/basics.md`
**Purpose**: CLI, Git, GitHub, 에디터 기초
**PPTX 연결**: PPTX 프로젝트 버전 관리 (Git)
**Reference**: `references/rev4-basics.md`

### 레볼루션 5: 스킬 만들기 실습
**Prompt**: `prompts/create-skill.md`
**Purpose**: 직접 Skill을 만들어보는 실습
**PPTX 연결**: `pptx-maker` 스킬 직접 제작
**Reference**: `references/rev5-create-skill.md`

### 레볼루션 6: 리서치와 학습의 결합
**Prompt**: `prompts/research-integration.md`
**Purpose**: domain-research 스킬과 학습 결합 활용법
**PPTX 연결**: 리서치 결과 → PPTX 자동 생성 파이프라인
**Reference**: `references/rev6-research-integration.md`

---

## 네비게이션

사용자가 학습을 시작하면:
1. 현재 수준을 파악 (초보/중급/고급)
2. 적절한 레볼루션부터 시작 제안
3. 레볼루션 번호 또는 "다음"/"이전"/"건너뛰기"로 이동

### 시작 명령어
```
/agentic-learning           # 처음부터 시작
/learn rev3                 # 특정 레볼루션으로 이동
/learn skill                # 스킬 만들기 실습
/learn research             # 리서치 결합 학습
/learn pptx                 # PPTX 프로젝트 현황 확인
```

---

## 학습 완료 후

학습을 마친 사용자는:
1. Claude Code의 7대 핵심 기능을 이해
2. PPTX 자동 생성 시스템을 3담당 구조로 구축 가능
3. 직접 Skill을 만들 수 있는 능력 보유
4. domain-research → PPTX 생성 파이프라인 구현 가능

---

## 연계 스킬

- **domain-research**: 학습 후 리서치 → PPTX 자동 생성에 적용
- **pptx**: PPTX 스킬 (레볼루션 5에서 직접 제작하는 스킬의 참고)
- **create-skill** (레볼루션 5): 나만의 스킬 제작 실습

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hongsw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
