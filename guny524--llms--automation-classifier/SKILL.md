---
name: automation-classifier
description: 사용자가 자동화하려는 작업을 Skill, Command, MCP, Subagent 중 적절한 도구로 분류하고 추천. 사용자가 "X 자동화 만들고 싶어", "이런 작업 자동화해줘", "도구 뭐 써야해?", "skill로 만들까 command로 만들까?" 같은 질문을 할 때 사용. Use when this capability is needed.
metadata:
  author: guny524
---

# 자동화 작업 분류 가이드

사용자가 자동화하려는 작업을 분석하여 Skill, Command, MCP, Subagent 중 가장 적합한 도구를 추천합니다.

## 1. 핵심 정의

### 1-1. Skill
- 정의: 도메인 특화 지식 패키지
- 형태: 폴더 (SKILL.md + 리소스)
- 트리거: 자동 (description 기반)
- 특징: 여러 파일, 예제, 참조 문서 포함 가능

### 1-2. Command
- 정의: 텍스트 단축키 프롬프트
- 형태: 단일 .md 파일
- 트리거: 수동 (/명령어)
- 특징: 빠른 실행, 인자 전달 가능

### 1-3. MCP (Model Context Protocol)
- 정의: 외부 시스템 연결 표준
- 형태: 서버-클라이언트 아키텍처
- 트리거: 자동 (도구 호출)
- 특징: API, DB, 외부 서비스 통합

### 1-4. Subagent
- 정의: 전문화된 AI 위임 어시스턴트
- 형태: 단일 .md 파일
- 트리거: 자동 또는 명시적
- 특징: 독립된 컨텍스트, 도구 접근 제어

## 2. Decision Tree

작업을 분석할 때 다음 순서로 질문하세요:

```
1. 외부 시스템/API 통합 필요?
   ├─ YES → MCP
   └─ NO ↓

2. 복잡한 작업을 독립된 컨텍스트에서 처리?
   ├─ YES → Subagent
   └─ NO ↓

3. 여러 파일/리소스 필요? 또는 도메인 특화 지식?
   ├─ YES → Skill
   └─ NO ↓

4. 빠른 프롬프트 실행?
   └─ YES → Command
```

## 3. 상황별 선택 가이드

| 상황 | 추천 도구 | 이유 |
|------|-----------|------|
| PDF를 마크다운으로 변환 (예제 파일 포함) | Skill | 예제 PDF/MD 파일 필요, 재사용 가능한 지식 |
| Git 커밋 메시지 작성 | Command | 단순 프롬프트, 수동 트리거 |
| Slack 메시지 전송 | MCP | 외부 API 연동 |
| 코드 리뷰 수행 | Subagent | 독립된 분석, 메인 컨텍스트 보존 |
| 회사 브랜드 가이드라인 적용 | Skill | 조직 지식, 자동 적용 |
| PR 생성 (commit + push + PR) | Command | 명시적 워크플로우, 순차 실행 |
| PostgreSQL 쿼리 | MCP | DB 접근 필요 |
| 디버깅 전용 조사 | Subagent | 전문화된 작업, 컨텍스트 분리 |

## 4. 판단 기준 체크리스트

### 4-1. Skill을 사용해야 할 때

조건:
- [ ] 여러 대화에서 반복적으로 재사용
- [ ] 도메인 특화 지식 필요 (예: 특정 문서 형식)
- [ ] 예제 파일이나 참조 문서 필요
- [ ] 자동 트리거 원함 (Claude가 상황 판단)
- [ ] 조직/팀에서 공유 필요

특징:
- Progressive disclosure로 토큰 효율적
- 자동 활성화 (description이 잘 작성되어야 함)
- 복잡한 리소스 포함 가능

예시:
- `payslip-converter`: PDF → MD 변환, 예제 포함
- `brand-guidelines`: 회사 브랜드 자동 적용
- `docx`, `pdf`, `xlsx`: 문서 처리 전문 기능

### 4-2. Command를 사용해야 할 때

조건:
- [ ] 단순 프롬프트 (1개 파일로 충분)
- [ ] 수동 트리거 필요 (`/commit` 처럼 명시적 실행)
- [ ] 빠른 실행이 목적
- [ ] 자주 타이핑하는 지침을 단축

특징:
- `/` 로 시작하는 명시적 호출
- 인자 전달 가능 (`$ARGUMENTS`, `$1`, `$2`)
- Bash 실행 가능 (`!ls`)
- 파일 참조 가능 (`@file.txt`)

예시:
- `/commit`: 커밋 메시지 작성
- `/code-review`: 코드 리뷰 시작
- `/document`: 문서화 실행

### 4-3. MCP를 사용해야 할 때

조건:
- [ ] 외부 시스템/API 통합 필요
- [ ] 데이터베이스 접근 필요
- [ ] 웹 서비스 연동 (Slack, GitHub, Gmail 등)
- [ ] 실시간 데이터 필요 (날씨, 주식 등)
- [ ] 표준화된 프로토콜 사용

특징:
- JSON-RPC 프로토콜
- 서버 구현 필요 (TypeScript/Python SDK 제공)
- Tools, Resources, Prompts 제공
- 보안 및 인증 처리

예시:
- `@modelcontextprotocol/server-slack`: Slack API
- `@modelcontextprotocol/server-postgres`: PostgreSQL
- `@modelcontextprotocol/server-github`: GitHub API
- `puppeteer-real-browser-mcp-server`: 웹 스크래핑

### 4-4. Subagent를 사용해야 할 때

조건:
- [ ] 복잡한 작업 위임 필요
- [ ] 독립된 컨텍스트 필요 (메인 대화 오염 방지)
- [ ] 전문화된 역할 필요 (코드 리뷰, 디버깅 등)
- [ ] 도구 접근 권한 분리 필요
- [ ] 병렬 처리 필요

특징:
- 독립된 context window
- 전용 시스템 프롬프트
- 도구 접근 제어 (`tools` 필드)
- 자동 또는 명시적 호출 모두 가능

예시:
- `code-reviewer`: 코드 품질/보안 검토
- `debugger`: 오류 분석 및 수정
- `data-scientist`: SQL 분석
- `code-explorer`: 기존 코드 분석

## 5. 혼합 사용 패턴

### 5-1. Skill + Command 조합
```
예: Payslip Converter

Skill (payslip-converter):
- PDF 형식 지식
- 예제 파일
- 변환 규칙

Command (/payslip):
- 빠른 변환 실행
- 파일 경로 인자 받기
- Skill 지식 활용
```

### 5-2. Command + Subagent 조합
```
예: Feature Development

Command (/feature-dev):
- 워크플로우 시작

Subagents:
- code-explorer: 기존 코드 분석
- code-architect: 아키텍처 설계
- code-reviewer: 코드 품질 검토
```

## 6. 분석 프로세스

사용자의 자동화 작업 요청을 받으면 다음 순서로 분석하세요:

### 6-1. 요구사항 파악
- 어떤 작업을 자동화하려는가?
- 어떤 입력/출력이 필요한가?
- 외부 시스템 연동이 필요한가?

### 6-2. Decision Tree 적용
- 4개 질문에 순차적으로 답변
- 각 질문의 답변 근거 명시

### 6-3. 도구 추천
- 가장 적합한 도구 1개 선택
- 선택 이유 명확히 설명
- 대안이 있다면 언급

### 6-4. 구조 제안
- 파일 구조
- 필수/선택 필드
- 구체적인 구현 가이드

### 6-5. 추가 고려사항
- 유지보수성
- 확장성
- 팀 공유 가능성

## 7. 출력 형식

다음 형식으로 추천 결과를 제공하세요:

```
## 분석 결과

추천 도구: [Skill/Command/MCP/Subagent]

### 선택 이유
1. [이유 1]
2. [이유 2]
3. [이유 3]

### 구조 제안
[파일/디렉토리 구조]

### 구현 가이드
[구체적인 구현 방법]

### 추가 고려사항
[유지보수, 확장성 등]
```

더 자세한 비교표는 [REFERENCE.md](~/llms/skills/automation-classifier/REFERENCE.md)를 참조하세요.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/guny524) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
