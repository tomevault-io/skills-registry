---
name: agent-creator
description: Claude Code 커스텀 에이전트(.claude/agents/ 마크다운 파일) 생성 가이드. 사용자가 서브에이전트로서 특정 도메인 전문성을 제공하는 새 에이전트를 만들거나 기존 에이전트를 업데이트할 때 사용. 프론트매터 설정, 시스템 프롬프트 설계, 도구 범위 지정, 모델 선택을 지원. Use when this capability is needed.
metadata:
  author: kubrickcode
---

# 에이전트 생성기

효과적인 Claude Code 커스텀 에이전트 생성 가이드.

## 에이전트란

에이전트는 도메인 전문성을 제공하는 서브에이전트 정의 파일(`.claude/agents/*.md`). 호출 시 에이전트의 마크다운 본문이 서브에이전트의 시스템 프롬프트가 된다.

**다른 설정 유형과의 차이:**

- **스킬**: 번들 리소스를 활용한 반복적 다단계 워크플로우 자동화
- **룰**: 코딩 표준 및 제약조건 강제 (항상 로딩됨)
- **에이전트**: 범위 지정된 도구와 모델로 도메인 전문가 페르소나 정의

## 핵심 원칙

### description이 핵심

`description` 필드가 자동 트리거의 핵심 메커니즘. Claude가 모든 에이전트 description을 읽고 위임을 결정한다. 행동 지향적 트리거 작성:

- 좋음: `"Database performance optimization specialist. Use PROACTIVELY for slow queries, indexing strategies, and execution plan analysis."`
- 나쁨: `"Helps with databases"`

### 단일 책임

하나의 에이전트, 하나의 도메인. 목적에 "그리고"가 필요하면 분리하라.

### 최소 권한 원칙

`tools`를 항상 명시적으로 지정. 도구 상속에 의존하지 않기 (생략 시 모든 도구 상속). 필요한 것만 부여.

### 토큰 경제성

에이전트 본문은 매 호출마다 시스템 프롬프트로 주입된다. 간결하게 유지 — 핵심 워크플로우와 역할 정의만 본문에 포함.

## 에이전트 생성 프로세스

1. 구체적 예시로 도메인 이해
2. 프론트매터 필드 설정
3. 에이전트 초기화 (init_agent.py 실행)
4. 시스템 프롬프트 본문 작성
5. 에이전트 검증 (validate_agent.py 실행)
6. 실사용 기반 반복 개선

### Step 1: 도메인 이해

파악할 사항:

- 이 에이전트가 처리해야 할 구체적 작업은?
- 사용자가 이 에이전트를 트리거할 만한 말은?
- 에이전트에 필요한 도구는?
- 도메인 복잡도는? (본문 크기 결정)

범위와 트리거가 명확해지면 완료.

### Step 2: 프론트매터 설정

필수 필드:

| 필드          | 용도                                                      |
| ------------- | --------------------------------------------------------- |
| `name`        | 파일명과 일치하는 kebab-case 식별자                       |
| `description` | 자동 트리거 텍스트. "Use PROACTIVELY when..." 트리거 포함 |

선택 필드:

| 필드              | 용도                                 | 기본값    |
| ----------------- | ------------------------------------ | --------- |
| `tools`           | 도구 허용 목록                       | 모든 도구 |
| `disallowedTools` | 도구 차단 목록                       | 없음      |
| `model`           | `haiku`, `sonnet`, `opus`, `inherit` | `inherit` |
| `maxTurns`        | 최대 에이전틱 턴 수                  | 무제한    |
| `skills`          | 시작 시 프리로딩할 스킬 (아래 참고)  | 없음      |
| `mcpServers`      | 사용 가능한 MCP 서버                 | 없음      |
| `permissionMode`  | 권한 동작                            | `default` |
| `memory`          | `user`, `project`, 또는 `local`      | 없음      |

**모델 선택 가이드:**

- `haiku`: 탐색, 검색, 단순 작업 (비용 효율적)
- `sonnet`: 코드 리뷰, 구현, 균형 잡힌 작업
- `opus`: 아키텍처 결정, 복잡한 추론
- `inherit`: 사용자 세션 모델과 동일 (권장 기본값)

**도구 선택 일반 조합:**

- 읽기 전용 리서치: `Read, Grep, Glob`
- 리서치 + 웹: `Read, Grep, Glob, WebFetch, WebSearch`
- 구현: `Read, Write, Edit, Bash, Glob, Grep`
- 풀 스택: `Read, Write, Edit, Bash, Glob, Grep, WebFetch, WebSearch`

**`skills` 필드 참고:**

`skills` 필드는 서브에이전트 스폰 시 스킬의 SKILL.md 콘텐츠를 시스템 프롬프트에 주입한다. 서브에이전트는 격리된 환경으로, 부모 대화의 컨텍스트를 볼 수 없기 때문에 이 필드를 통해 도메인 지식을 제공한다.

**자동 위임 서브에이전트**(Claude가 Task 도구로 스폰) 또는 **`claude --agent` / Agent Teams** 모드에서만 의미가 있다. 수동 호출 워크플로우(대화에서 `@agent-name` 사용)에서는 메인 대화가 이미 전체 컨텍스트를 가지고 있으므로 `skills`가 불필요하다.

고급 프론트매터 필드와 상세 패턴은 [references/agent-patterns.md](references/agent-patterns.md) 참조.

### Step 3: 에이전트 초기화

초기화 스크립트를 실행하여 템플릿 에이전트 생성:

```bash
scripts/init_agent.py <agent-name> --path <output-directory>
```

스크립트가 적절한 프론트매터와 TODO 플레이스홀더가 포함된 `.md` 파일을 생성한다.

기존 에이전트 업데이트 시 이 단계를 건너뛰기.

### Step 4: 시스템 프롬프트 본문 작성

프론트매터 이후의 마크다운 본문이 서브에이전트의 시스템 프롬프트가 된다. 도메인 복잡도에 따라 구조화:

**최소 (30-40줄)** — 좁고 집중된 도메인:

```markdown
You are an expert [ROLE] specializing in [DOMAIN].

When invoked:

1. [Step 1]
2. [Step 2]
3. [Step 3]

For each task, provide:

- [Deliverable 1]
- [Deliverable 2]

Focus on [key principle].
```

**표준 (80-140줄)** — 대부분의 에이전트:

```markdown
You are a [ROLE] with expertise in [DOMAIN].

## Core Workflow

When invoked:

1. [Step 1]
2. [Step 2]
3. [Step 3]

## [Domain Area 1]

- [Specific guidance]
- [Decision criteria]

## [Domain Area 2]

- [Patterns and approaches]

## Tool Selection

Essential tools:

- **Read/Grep**: [Purpose]
- **Edit/Write**: [Purpose]

Collaboration:

- **[other-agent]**: [When to delegate]

## Output Format

[Template or example of expected output]

## Key Principles

- [Principle 1]
- [Principle 2]
```

**포괄 (150줄 이상)** — 다단계 페이즈가 있는 복잡한 도메인:

- 의사결정 테이블/매트릭스 추가
- 페이즈 기반 워크플로우 추가
- 안티패턴 목록 추가
- 성공 기준 추가

상세 본문 구조 템플릿은 [references/agent-patterns.md](references/agent-patterns.md) 참조.

**작성 지침:**

- 첫 줄: `You are a [ROLE]...` — 전문성 설정
- 지시문은 명령형 사용
- 순차 프로세스는 번호 매기기
- 옵션/체크리스트는 불릿 포인트
- 의사결정 프레임워크는 테이블
- 메인 섹션 H2, 서브섹션 H3 (H4 사용 금지)
- "Tool Selection" 섹션에 근거 포함
- 핵심 원칙 또는 성공 기준으로 마무리

### Step 5: 에이전트 검증

검증 스크립트 실행:

```bash
scripts/validate_agent.py <path/to/agent.md>
```

검증 항목:

- YAML 프론트매터 형식 및 필수 필드
- description 품질 및 길이
- 도구 이름 유효성
- 모델 값 유효성
- 본문 구조 규약

### Step 6: 반복 개선

1. 실제 작업을 에이전트에 위임하여 테스트
2. 어려움이나 예상치 못한 출력 관찰
3. 시스템 프롬프트, 도구 범위, 모델 선택 개선
4. 변경 후 재검증

## 이중 언어 프로젝트

프로젝트에 `.claude/agents/`와 `.claude.ko/agents/`가 모두 있는 경우:

- 두 버전을 동시에 생성
- 영문 버전: `.claude/agents/<name>.md`
- 한국어 버전: `.claude.ko/agents/<name>.md`
- 동일한 구조와 로직 유지, 콘텐츠만 번역

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kubrickcode) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
