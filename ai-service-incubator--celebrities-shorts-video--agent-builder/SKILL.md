---
name: agent-builder
description: Agent Builder - 에이전트 생성, 분할, 팀 구성을 위한 빌더 시스템. 에이전트를 프로그래밍 방식으로 관리합니다. Use when this capability is needed.
metadata:
  author: ai-service-incubator
---

# Agent Builder Skill

에이전트를 프로그래밍 방식으로 생성하고 관리하는 빌더 시스템입니다.

## 사용법

```
/agent-builder [command] [options]
```

## 명령어

### 1. list - 에이전트 목록 조회

```
/agent-builder list
/agent-builder list --type agents
/agent-builder list --type teams
/agent-builder list --type sub-agents
/agent-builder list --type all
```

**실행 과정:**
1. `.claude/builder/registry.json` 읽기
2. 요청 타입별 필터링
3. 테이블 형태로 출력

**출력 예시:**
```
═══════════════════════════════════════════════
           AGENT REGISTRY
═══════════════════════════════════════════════

📦 STANDALONE AGENTS (4)
┌────────────────────┬────────────────────────────┐
│ Name               │ Description                │
├────────────────────┼────────────────────────────┤
│ script-writer      │ 60초 숏폼 스크립트 작성     │
│ fact-checker       │ 제품 정보 검증             │
│ trend-analyst      │ 트렌드 분석                │
│ thumbnail-designer │ 썸네일 디자인              │
└────────────────────┴────────────────────────────┘

🔧 SUB-AGENTS (8)
┌─────────────────┬─────────────────┬─────────────────────────┐
│ Name            │ Parent Team     │ Description             │
├─────────────────┼─────────────────┼─────────────────────────┤
│ sns-discoverer  │ research-team   │ SNS 채널 탐색            │
│ content-analyzer│ research-team   │ 콘텐츠 분석              │
│ ...             │ ...             │ ...                     │
└─────────────────┴─────────────────┴─────────────────────────┘

👥 TEAMS (3)
┌─────────────────┬─────────────────────┬───────────────────────┐
│ Name            │ Members             │ Routing               │
├─────────────────┼─────────────────────┼───────────────────────┤
│ research-team   │ 3 agents            │ sequential            │
│ production-team │ 6 agents            │ sequential            │
│ publishing-team │ 3 agents            │ sequential            │
└─────────────────┴─────────────────────┴───────────────────────┘
```

---

### 2. create - 에이전트 생성

```
/agent-builder create [agent-name] --template [base|sub-agent|team]
```

**옵션:**
- `--template`: 사용할 템플릿 (base, sub-agent, team)
- `--parent`: 부모 팀 (sub-agent일 경우)
- `--tools`: 사용할 도구들 (쉼표 구분)

**예시:**
```
/agent-builder create my-agent --template base --tools Read,Write
/agent-builder create my-sub-agent --template sub-agent --parent research-team
/agent-builder create my-team --template team
```

**실행 과정:**
1. 템플릿 파일 로드 (`.claude/builder/templates/`)
2. 사용자 입력으로 플레이스홀더 치환
3. 에이전트 파일 생성
4. `registry.json` 업데이트

---

### 3. split - 에이전트 분할

```
/agent-builder split [source-agent] --into [new-agent-1],[new-agent-2]
```

**옵션:**
- `--into`: 분할할 새 에이전트 이름들 (쉼표 구분)
- `--team`: 자동으로 팀 생성 여부

**예시:**
```
/agent-builder split content-researcher --into sns-discoverer,content-analyzer
/agent-builder split video-producer --into edit-director,video-renderer --team production-team
```

**실행 과정:**
1. 소스 에이전트 파일 읽기
2. 역할/도구 분석
3. 분할된 서브 에이전트 파일 생성
4. (옵션) 팀 에이전트 생성
5. 원본 에이전트 deprecated 처리
6. `registry.json` 업데이트

---

### 4. team - 팀 관리

```
/agent-builder team create [team-name] --members [agent1],[agent2],[agent3]
/agent-builder team add [team-name] --member [agent-name]
/agent-builder team remove [team-name] --member [agent-name]
/agent-builder team show [team-name]
```

**예시:**
```
/agent-builder team create my-team --members agent1,agent2,agent3
/agent-builder team add research-team --member new-agent
/agent-builder team show production-team
```

**실행 과정:**
1. 멤버 에이전트 존재 여부 확인
2. 팀 에이전트 파일 생성/수정
3. 라우팅 전략 설정
4. `registry.json` 업데이트

---

### 5. validate - 검증

```
/agent-builder validate
/agent-builder validate [agent-name]
/agent-builder validate --all
```

**검증 항목:**
- 에이전트 파일 존재 여부
- YAML frontmatter 유효성
- 도구 목록 유효성
- 팀 멤버 참조 유효성
- 순환 의존성 체크

**출력 예시:**
```
═══════════════════════════════════════════════
           VALIDATION REPORT
═══════════════════════════════════════════════

✅ script-writer: VALID
✅ sns-discoverer: VALID
✅ research-team: VALID
   └─ Members: 3/3 valid
   └─ Routing: sequential ✓

⚠️  WARNINGS (1)
   └─ caption-writer: Missing parent-team reference

❌ ERRORS (0)

Overall: PASS (15/15 valid, 1 warning, 0 errors)
```

---

### 6. info - 상세 정보

```
/agent-builder info [agent-name]
```

**출력 정보:**
- 기본 정보 (이름, 타입, 설명)
- 도구 목록
- 팀 소속 (해당 시)
- 의존성
- 파일 경로

---

## 레지스트리 관리

### registry.json 구조

```json
{
  "version": "1.0.0",
  "last_updated": "YYYY-MM-DD",
  "agents": {
    "standalone": [...],
    "sub-agents": [...],
    "teams": [...]
  },
  "relationships": {
    "pipeline": ["team1", "team2", "team3"],
    "dependencies": {...}
  },
  "deprecated": [...]
}
```

### 레지스트리 동기화

```
/agent-builder sync
```

실제 파일과 registry.json을 동기화합니다.

---

## 템플릿 시스템

### 사용 가능한 템플릿

| 템플릿 | 경로 | 용도 |
|--------|------|------|
| team-agent | `.claude/builder/templates/team-agent.md` | 팀/게이트 에이전트 |
| sub-agent | `.claude/builder/templates/sub-agent.md` | 서브 에이전트 |

### 플레이스홀더

| 플레이스홀더 | 설명 |
|-------------|------|
| `{{TEAM_NAME}}` | 팀 이름 |
| `{{AGENT_NAME}}` | 에이전트 이름 |
| `{{DESCRIPTION}}` | 설명 |
| `{{TOOLS}}` | 도구 목록 |
| `{{PARENT_TEAM}}` | 부모 팀 |
| `{{MEMBERS}}` | 팀 멤버 목록 |

---

## 실행 예시

### 새 서브 에이전트 생성

```
사용자: /agent-builder create price-checker --template sub-agent --parent research-team --tools WebSearch,WebFetch

Agent Builder 응답:
✅ 에이전트 생성 완료!

📄 파일: .claude/agents/price-checker.md
👥 팀: research-team
🔧 도구: WebSearch, WebFetch

다음 단계:
1. .claude/agents/price-checker.md 편집하여 상세 지시사항 추가
2. /agent-builder validate price-checker 로 검증
```

### 팀 구조 확인

```
사용자: /agent-builder team show research-team

Agent Builder 응답:
═══════════════════════════════════════════════
         RESEARCH-TEAM
═══════════════════════════════════════════════

📋 기본 정보
   파일: .claude/teams/research-team.md
   타입: team (coordinator)
   라우팅: sequential

👥 멤버 (3)
   1. sns-discoverer (primary)
   2. content-analyzer (secondary)
   3. fact-checker (verifier)

🔄 플로우
   sns-discoverer → content-analyzer → fact-checker

📦 산출물
   └─ data/research/[연예인명]_research_complete.json
```

---

## 주의사항

1. 에이전트 생성 후 반드시 상세 지시사항 편집 필요
2. 팀 멤버 변경 시 라우팅 순서 확인
3. deprecated 에이전트는 삭제하지 않고 기록 유지
4. registry.json 수동 편집 시 sync 명령 실행 권장

---

## 관련 파일

| 파일 | 설명 |
|------|------|
| `.claude/builder/registry.json` | 에이전트 레지스트리 |
| `.claude/builder/templates/` | 템플릿 폴더 |
| `.claude/agents/` | 개별 에이전트 |
| `.claude/teams/` | 팀 에이전트 |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ai-service-incubator) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
