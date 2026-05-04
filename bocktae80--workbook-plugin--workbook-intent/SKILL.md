---
name: workbook-intent
description: Route natural language to the correct workbook skill or agent. This is the central router — use ONLY when the user's natural language request is ambiguous and could map to multiple skills, or when they ask a general workbook question without targeting a specific domain. Do NOT use if a domain skill (wb-session, wb-plan, wb-quality, wb-team, wb-observe, wb-infra, wb-knowledge, wb-admin, wb-ship, wb-auto, wb-cockpit, wb-scout, wb-notice, wb-github) clearly matches. Triggers on: '워크북 뭐 할 수 있어?', '워크북 기능 안내', '도움말', '워크북 도움', '워크북 사용법', '워크북 기능 목록', 'what can workbook do', 'workbook help', 'workbook overview', 'workbook features', 'help me with workbook', 'show workbook capabilities', 'list workbook skills'. Also delegates to bundled external skills (React performance, UI review) when relevant keywords match. NOT for: specific skill operations (use the domain skill directly), plugin marketplace (use wb-scout). Use when this capability is needed.
metadata:
  author: bocktae80
---

# Workbook Intent Skill

사용자의 자연어 입력을 적절한 워크북 스킬 또는 에이전트로 라우팅하는 스킬입니다.

## 개요

이 스킬은 사용자가 자연어로 워크북 관련 요청을 할 때 자동으로 활성화됩니다.
2단계 도메인 라우팅으로 동작합니다:
1. **1단계**: 키워드 → 도메인 스킬/에이전트 식별
2. **2단계**: 스킬 내부에서 `$ARGUMENTS`로 서브커맨드 분기

## 스킬 3계층

| 계층 | 한글 | 정의 |
|------|------|------|
| **builtin** | 내장 | 워크북 플러그인 자체 스킬 (`/wb-*`). 삭제 불가 |
| **bundled** | 번들 | 워크북이 자동 설치·보장하는 외부 스킬 |
| **recommended** | 추천 | 프로젝트 컨텍스트 기반 추천. 미설치, 사용자 선택 |

> 상세: `${CLAUDE_PLUGIN_ROOT}/skills-deps.json`

## 번들 스킬 위임 (Phase 0 — 도메인 라우팅 전 실행)

워크북은 bundled 스킬을 하위 도구로 활용합니다.
사용자 입력이 아래 위임 키워드에 매칭되면, 워크북이 해당 스킬을 로드하여 오케스트레이션합니다.

### 위임 테이블

| ID | 키워드 (한글) | 키워드 (영어) | 위임 스킬 | 계층 |
|----|--------------|--------------|-----------|------|
| react-performance | React 성능, 렌더링 최적화, 번들 사이즈, 서버 컴포넌트, 워터폴 | react perf, bundle size, server component, waterfall | `vercel-react-best-practices` | bundled |
| ui-review | UI 리뷰, 접근성, 디자인 검토, UX 점검 | design review, accessibility audit, UI review | `web-design-guidelines` | bundled |
| simplify | 코드 정리, 코드 간소화, 리팩토링, 코드 개선 | simplify, code cleanup, refactor code, code improvement | 시스템 내장 `/simplify` | builtin |

### 위임 실행 절차

1. 스킬 설치 확인:
   - Glob: `~/.claude/skills/{skillName}/SKILL.md` 또는 `~/.agents/skills/{skillName}/SKILL.md`
2. **설치됨** (bundled 스킬은 SessionStart에서 자동 보장):
   - Read: 해당 SKILL.md
   - 워크북 컨텍스트(프로젝트 규칙, 에센스)와 결합하여 실행
   - 출력 형식: `[{skillName}] {실행 결과}`
3. **미설치** (bundled 설치 실패 또는 recommended 스킬):
   - 워크북 내부 지식으로 폴백 처리
   - 안내: `"{skillName}" 스킬을 설치하면 더 전문적인 분석이 가능합니다.`
   - `npx skills add {repo} -s {skillName} -g -y`

## 복합 인텐트 (Multi-Skill Sequence)

단일 자연어 입력이 여러 스킬의 순차 실행으로 매핑되는 경우입니다.
복합 인텐트는 도메인 라우팅보다 **먼저** 매칭됩니다.

| 입력 | 실행 순서 | 조건 |
|------|----------|------|
| "마무리 해줘", "마무리", "wrap up" | `/wb-ship` → `/wb-session end` | ship 시 변경사항 없으면 session end만 |
| "쉽하고 마무리", "ship and end" | `/wb-ship` → `/wb-session end` | 명시적 조합 |

**"세션 마무리"와의 구분**: "세션"이라는 스코프 한정어가 있으면 `/wb-session end`만 실행합니다.

```typescript
const compoundPatterns = [
  {
    id: 'ship-and-end',
    triggers: [
      /^마무리/, // "마무리 해줘", "마무리할게"
      /마무리\s*(해줘|할게|하자|합시다)/i,
      /wrap\s*up/i,
      /쉽하고\s*마무리/i,
      /ship\s*(and|&)\s*end/i,
      /반영하고\s*마무리/i,
      /오늘\s*(끝|마무리|마감)/i,
      /다\s*(했어|끝났어|됐어)/i,
      /done\s*for\s*(today|now)/i,
      /커밋.*마무리|마무리.*커밋/i,
      /쉽핑.*마무리|마무리.*쉽핑/i,
    ],
    // "세션 마무리"는 제외 — 세션 도메인으로 폴스루
    excludes: [/세션\s*마무리/i],
    sequence: ['/wb-ship', '/wb-session end'],
    conditions: {
      // ship 시 변경사항이 없으면 ship 건너뛰고 session end만
      skipFirst: 'git status에 변경사항 없으면 /wb-ship 건너뜀',
    },
  },
];

// Phase 0.5: 복합 인텐트 체크 (위임 후, 도메인 라우팅 전)
function checkCompound(input: string): CompoundMatch | null {
  for (const compound of compoundPatterns) {
    // exclude 패턴에 걸리면 건너뜀
    if (compound.excludes?.some(ex => ex.test(input))) continue;
    if (compound.triggers.some(t => t.test(input))) {
      return {
        type: 'compound',
        id: compound.id,
        sequence: compound.sequence,
        conditions: compound.conditions,
      };
    }
  }
  return null;
}

/*
실행 순서 (업데이트):
  0.  Phase 0: checkDelegation(input) → 외부 스킬 위임
  0.5 Phase 0.5: checkCompound(input) → 복합 인텐트 (multi-skill)
  1.  Phase 1: detectIntent(input) → 워크북 도메인 라우팅
*/
```

## 도메인 라우팅 테이블

| 도메인 | 키워드 (한글) | 키워드 (영어) | 스킬/에이전트 |
|--------|--------------|--------------|--------------|
| 세션 | 작업 시작/끝, 세션, 체크포인트, 저장 | session, start/end work, checkpoint | `/wb-session` |
| 플랜 | 플랜, 로드맵, 마일스톤, 백로그 | plan, roadmap, milestone, backlog | `/wb-plan` |
| 지식 | 에센스, 인사이트, 메모리, 컨텍스트 | essence, insight, memory, context | `/wb-knowledge` |
| 품질 | 패스루프, 리뷰, 레드팀, 프루프라인 | passloop, review, redteam, proofline | `/wb-quality` |
| 팀 | 팀, TeamKit, 팀 스폰 | team, spawn team | `/wb-team` |
| 관측 | 상태, 분석, 워크로그, 변경기록 | status, analytics, worklog, log | `/wb-observe` |
| 인프라 | 키스톤, 콘솔, 플레이그라운드 | keystone, console, playground | `/wb-infra` |
| 관리 | 초기화, 텔레메트리, 피드백 | init, telemetry, feedback | `/wb-admin` |
| 미션 | 미션 루프, 미션 파이프라인 | mission loop | agent `wb-mission` |
| 진화 | 진화, 규칙 제안 | evolve, evolution | agent `wb-evolve` |
| 워커 | 워커, 자율실행 | worker | agent `wb-worker` |
| 알림 | 알림, 공지, 노티스, 팀 알림, 변경 알림 | notice, notification, broadcast, announce | `/wb-notice` |
| 반영 | 쉽, 반영해줘, 커밋하고 정리, ship | ship, commit and update | `/wb-ship` |
| 자동화 | 자동화, 자동화 상태, 신호, 기준, 후보 | automation, auto, signals, criteria | `/wb-auto` |
| 콕피트 | 콕피트, 전체 현황, 워크스페이스 상태 | cockpit, workspace status, workspace overview | `/wb-cockpit` |
| 스카우트 | 플러그인 추천, 플러그인 찾기, 마켓플레이스 | plugin recommend, find plugins, marketplace | `/wb-scout` |
| 깃허브 | 깃허브, 깃허브 연동, GitHub 동기화 | github, github sync, github status | `/wb-github` |

## 서브커맨드 라우팅 (2단계)

### 세션 (`/wb-session`)
| 서브커맨드 | 키워드 (한글) | 키워드 (영어) |
|-----------|--------------|--------------|
| (기본) | 작업 시작, 세션 시작, 워크북 | start work, begin session |
| `end` | 작업 끝, 세션 종료, 세션 마무리 | end work, finish session, end session |
| `checkpoint` | 저장, 체크포인트, 중간 저장 | checkpoint, save progress |

### 플랜 (`/wb-plan`)
| 서브커맨드 | 키워드 (한글) | 키워드 (영어) |
|-----------|--------------|--------------|
| (기본) | 플랜 보여줘, 계획 확인, 마일스톤, 로드맵 | show plan, roadmap |
| `add` | 마일스톤 추가, 마일스톤 만들어줘 | add milestone |
| `review` | 플랜 리뷰, 플랜 검토, 계획 리뷰 | plan review, review plan |
| `backlog` | 백로그, 나중에 할 일 | backlog, show backlog |
| `backlog add` | 백로그 추가, 백로그에 넣어줘 | add backlog, add to backlog |
| `backlog remove` | 백로그 제거, 백로그에서 빼줘 | remove backlog |

### 지식 (`/wb-knowledge`)
| 서브커맨드 | 키워드 (한글) | 키워드 (영어) |
|-----------|--------------|--------------|
| (기본) | 에센스, 인사이트, 교훈 | essence, insight, lessons |
| `add` | 에센스 추가, 인사이트 추가 | add essence, add insight |
| `promote` | 에센스 승격, 성숙도 올리기 | promote essence |
| `memory` | 메모리, 프로젝트 기억, 기억 보여줘 | memory, project memory |
| `context check` | 컨텍스트 점검, 맥락 점검 | context check |
| `context examples` | 예시 조회, few-shot | examples, few-shot |
| `context role` | 역할 추천, 역할 매칭 | role match |
| `context bias` | 편향 점검, 편향 스캔 | bias check |
| `context cot` | 사고 단계, 사고 체인 | cot, chain of thought |
| `discover` | 스킬 탐색, 스킬 현황, 외부 스킬 | skill discover, skill list |
| `discover add` | 스킬 추가, 추천에 등록 | add skill |
| `discover bundle` | 스킬 번들, 스킬 승격 | bundle skill |
| `discover unbundle` | 번들 해제, 스킬 강등 | unbundle skill |
| `discover remove` | 스킬 제거, 스킬 삭제 | remove skill |
| `discover install` | 스킬 설치 | install skill |

### 품질 (`/wb-quality`)
| 서브커맨드 | 키워드 (한글) | 키워드 (영어) |
|-----------|--------------|--------------|
| `passloop` | 패스루프, 게이트, 품질 검증 | passloop, gate |
| `review` | 리뷰, 품질 리뷰 | review, quality review |
| `redteam` | 레드팀 평가, RTS 평가 | red team evaluate, rts check |
| `redteam loop` | 레드팀 루프, 레드팀 반복 | red team loop |
| `redteam report` | 레드팀 결과, RTS 결과 | red team report |
| `proofline` | 프루프라인, 품질 파이프라인 | proofline |

### 팀 (`/wb-team`)
| 서브커맨드 | 키워드 (한글) | 키워드 (영어) |
|-----------|--------------|--------------|
| (기본) | 팀, TeamKit, 팀 목록 | team, team templates |
| `spawn` | 팀 만들어줘, 팀 스폰 | spawn team, create team |
| `create` | TeamKit 만들기, 커스텀 팀 | create template, custom team |

### 관측 (`/wb-observe`)
| 서브커맨드 | 키워드 (한글) | 키워드 (영어) |
|-----------|--------------|--------------|
| (기본) | 현재 상태, 진행 상황, 상태 | status, progress |
| `log` | 변경 기록, 로그 추가 | add log, record change |
| `worklog` | 워크로그, 작업 로그, 작업 이력 | worklog, work log |
| `worklog search` | 워크로그 검색, 작업 이력 검색 | search worklog |
| `analytics` | 분석, 통계, 사용 통계 | analytics, stats, usage |

### 인프라 (`/wb-infra`)
| 서브커맨드 | 키워드 (한글) | 키워드 (영어) |
|-----------|--------------|--------------|
| `console` | 콘솔, 대시보드, 웹 UI | console, dashboard, web ui |
| `playground` | 플레이그라운드, 인터랙티브 뷰 | playground, interactive view |
| `playground roadmap-explorer` | 로드맵 시각화, 마일스톤 시각화 | roadmap playground |
| `playground knowledge-map` | 지식 맵, 에센스 맵 | knowledge map |
| `playground session-timeline` | 세션 타임라인, 세션 히트맵 | session timeline |
| `playground team-composer` | 팀 빌더, 팀 시각화 | team composer, team builder |

### 알림 (`/wb-notice`)
| 서브커맨드 | 키워드 (한글) | 키워드 (영어) |
|-----------|--------------|--------------|
| (기본), `list` | 알림 확인, 알림 목록 | list notices, show notices |
| `create` | 알림 생성, 공지 올려줘, 팀한테 알려줘 | create notice, broadcast |
| `ack` | 알림 확인 처리, 읽음 | ack notice, acknowledge |
| `expire` | 알림 만료, 알림 정리 | expire notice, cleanup |

### 관리 (`/wb-admin`)
| 서브커맨드 | 키워드 (한글) | 키워드 (영어) |
|-----------|--------------|--------------|
| `init` | 워크북 초기화, 워크북 만들어줘 | init workbook, create workbook |
| `telemetry` | 텔레메트리, 데이터 수집 | telemetry, data collection |
| `feedback` | 피드백, 의견 제출, 버그 신고 | feedback, report bug |

### 에이전트 (Task 도구로 호출)
| 에이전트 | 키워드 (한글) | 키워드 (영어) |
|---------|--------------|--------------|
| `wb-mission` | 미션 루프, 미션 파이프라인, 팀 미션 | mission loop, mission pipeline |
| `wb-evolve` | 진화, 자동 진화, 규칙 제안 | evolve, evolution, rule suggestion |
| `wb-worker` | 워커, 자율실행 | worker, start worker |

## 인텐트 감지 로직

```typescript
interface IntentMatch {
  intent: string;
  skill: string;        // 스킬 경로 또는 agent:{name}
  subcommand?: string;  // 서브커맨드 (있으면)
  confidence: number;
  extractedArgs?: string;
}

interface DelegationMatch {
  type: 'delegation';
  id: string;
  skillName: string;
  confidence: number;
}

// Phase 0: 외부 스킬 위임
const delegationPatterns = [
  {
    id: 'react-performance',
    skillName: 'vercel-react-best-practices',
    patterns: [
      /React\s*(성능|최적화|렌더링)/i,
      /번들\s*사이즈/i,
      /서버\s*컴포넌트/i,
      /워터폴\s*(제거|방지)/i,
      /react\s*(perf|performance|optimiz)/i,
      /bundle\s*size/i,
      /server\s*component/i,
    ],
  },
  {
    id: 'ui-review',
    skillName: 'web-design-guidelines',
    patterns: [
      /UI\s*(리뷰|검토|감사|점검)/i,
      /접근성\s*(검토|체크|감사)/i,
      /디자인\s*(리뷰|검토|감사)/i,
      /UX\s*(점검|리뷰)/i,
      /(design|ui)\s*(review|audit)/i,
      /accessibility\s*(check|audit)/i,
    ],
  },
  {
    id: 'simplify',
    skillName: '/simplify',  // 시스템 내장 스킬 — 직접 위임
    patterns: [
      /코드\s*(정리|간소화|개선|클린업)/i,
      /리팩토링/i,
      /코드\s*품질\s*(개선|향상)/i,
      /simplify/i,
      /code\s*(cleanup|simplif|improv)/i,
      /refactor\s*(code|this)/i,
    ],
  },
];

function checkDelegation(input: string): DelegationMatch | null {
  for (const rule of delegationPatterns) {
    for (const pattern of rule.patterns) {
      if (pattern.test(input)) {
        // Glob으로 스킬 설치 확인
        // 설치됨 → Read SKILL.md → 워크북 오케스트레이션 하에 실행
        // 미설치 → null 반환 → 기존 도메인 라우팅으로 폴백
        return {
          type: 'delegation',
          id: rule.id,
          skillName: rule.skillName,
          confidence: 0.9,
        };
      }
    }
  }
  return null;
}

/*
실행 순서:
  0.  Phase 0: checkDelegation(input) → 외부 스킬 위임 (설치 시)
  0.5 Phase 0.5: checkCompound(input) → 복합 인텐트 (multi-skill)
  1.  Phase 1: detectIntent(input) → 워크북 도메인 라우팅 (기존)
*/

// 1단계: 도메인 라우팅
const domainPatterns = [
  {
    domain: 'session',
    skill: '/wb-session',
    patterns: [
      /작업\s*(시작|끝|종료)/i,
      /세션\s*(시작|종료|끝|마무리)/i,
      /워크북/i,
      /저장|체크포인트|중간\s*저장/i,
      /(start|end|begin|finish)\s*(work|session)/i,
      /checkpoint|save\s*progress/i,
    ],
    subcommands: [
      { sub: 'end', patterns: [/작업\s*(끝|종료)/i, /세션\s*(끝|종료|마무리)/i, /(end|finish)\s*(work|session)/i] },
      { sub: 'checkpoint', patterns: [/저장/i, /체크포인트/i, /중간\s*저장/i, /checkpoint/i, /save\s*progress/i] },
    ],
  },
  {
    domain: 'plan',
    skill: '/wb-plan',
    patterns: [
      /플랜|계획|마일스톤|로드맵/i,
      /백로그|나중에\s*할\s*(일|것)/i,
      /plan|roadmap|milestone|backlog/i,
    ],
    subcommands: [
      { sub: 'add', patterns: [/마일스톤\s*(추가|만들어|생성)/i, /(add|create|new)\s*milestone/i] },
      { sub: 'review', patterns: [/플랜\s*(리뷰|검토)/i, /계획\s*(리뷰|검토)/i, /plan\s*review/i, /review\s*plan/i] },
      { sub: 'backlog add', patterns: [/백로그\s*(에\s*)?(추가|넣어|등록)/i, /나중에\s*할\s*(일|것)\s*(추가|넣어)/i, /add\s*(to\s*)?backlog/i] },
      { sub: 'backlog remove', patterns: [/백로그\s*(에서\s*)?(제거|빼|삭제|지워)/i, /(remove|delete)\s*(from\s*)?backlog/i] },
      { sub: 'backlog', patterns: [/백로그/i, /나중에\s*할\s*(일|것)/i, /backlog/i] },
    ],
  },
  {
    domain: 'knowledge',
    skill: '/wb-knowledge',
    patterns: [
      /에센스|인사이트|교훈/i,
      /메모리|프로젝트\s*기억|기억\s*(보여|확인)/i,
      /컨텍스트\s*(점검|분석|확인)|맥락\s*(점검|분석)/i,
      /예시\s*(조회|검색)|few[\s-]?shot/i,
      /역할\s*(추천|매칭)/i,
      /편향\s*(점검|스캔|분석)/i,
      /사고\s*(단계|체인)|cot/i,
      /essence|insight|lessons?|memory|context/i,
    ],
    subcommands: [
      { sub: 'add', patterns: [/에센스\s*(추가|생성)/i, /인사이트\s*추가/i, /교훈\s*(기록|추가)/i, /add\s*(essence|insight)/i] },
      { sub: 'promote', patterns: [/에센스\s*승격/i, /성숙도\s*(올리기|승격)/i, /규칙으로\s*(변환|승격)/i, /promote\s*(essence|maturity)/i] },
      { sub: 'memory', patterns: [/메모리/i, /프로젝트\s*기억/i, /기억\s*(보여|확인)/i, /project\s*memory/i] },
      { sub: 'context check', patterns: [/컨텍스트\s*(점검|분석|확인)/i, /맥락\s*(점검|분석)/i, /context\s*(check|analysis)/i] },
      { sub: 'context examples', patterns: [/예시\s*(조회|검색|보여)/i, /few[\s-]?shot/i, /context\s*examples/i] },
      { sub: 'context role', patterns: [/역할\s*(추천|매칭|검색)/i, /role\s*(match|resolve)/i] },
      { sub: 'context bias', patterns: [/편향\s*(점검|스캔|분석)/i, /bias\s*(check|scan)/i] },
      { sub: 'context cot', patterns: [/사고\s*(단계|체인)/i, /cot/i, /chain\s*of\s*thought/i] },
      { sub: 'discover', patterns: [/스킬\s*(탐색|추천|검색|발견|목록|현황)/i, /외부\s*스킬/i, /어떤\s*스킬/i, /skills?\s*(discover|find|recommend|search|list)/i] },
      { sub: 'discover add', patterns: [/스킬\s*(추가|등록)/i, /추천\s*(에\s*)?(추가|등록)/i, /add\s*skill/i] },
      { sub: 'discover bundle', patterns: [/스킬\s*(번들|번들링|승격)/i, /번들\s*(에\s*)?(추가|등록|승격)/i, /bundle\s*skill/i] },
      { sub: 'discover unbundle', patterns: [/스킬\s*(번들\s*해제|강등)/i, /번들\s*(에서\s*)?(제거|해제|강등)/i, /unbundle\s*skill/i] },
      { sub: 'discover remove', patterns: [/스킬\s*(제거|삭제)/i, /remove\s*skill/i, /delete\s*skill/i] },
      { sub: 'discover install', patterns: [/스킬\s*(설치|인스톨)/i, /install\s*skill/i] },
    ],
  },
  {
    domain: 'quality',
    skill: '/wb-quality',
    patterns: [
      /패스루프|게이트/i,
      /리뷰|품질\s*리뷰/i,
      /레드팀|RTS/i,
      /프루프라인/i,
      /passloop|gate|review|red\s*team|rts|proofline/i,
    ],
    subcommands: [
      { sub: 'passloop', patterns: [/패스루프/i, /게이트/i, /passloop/i, /gate/i] },
      { sub: 'proofline', patterns: [/프루프라인/i, /proofline/i] },
      { sub: 'redteam loop', patterns: [/레드팀\s*(루프|반복|순환)/i, /red\s*team\s*loop/i] },
      { sub: 'redteam report', patterns: [/레드팀\s*(결과|리포트|보고)/i, /RTS\s*(결과|리포트)/i, /red\s*team\s*(report|result)/i] },
      { sub: 'redteam', patterns: [/레드팀\s*(평가|돌려|실행|검증)/i, /RTS\s*(평가|검증|체크)/i, /red\s*team\s*(evaluate|check|run)/i] },
      { sub: 'review', patterns: [/리뷰/i, /품질\s*리뷰/i, /review/i] },
    ],
  },
  {
    domain: 'team',
    skill: '/wb-team',
    patterns: [
      /팀\s*(목록|조회|보여줘|템플릿|만들어|스폰|생성|시작)/i,
      /TeamKit/i,
      /커스텀\s*팀/i,
      /team\s*(list|templates?|spawn|create)/i,
    ],
    subcommands: [
      { sub: 'create', patterns: [/팀\s*템플릿\s*(만들기|생성|추가)/i, /커스텀\s*팀/i, /create\s*team\s*template/i, /custom\s*team/i] },
      { sub: 'spawn', patterns: [/팀\s*(만들어|스폰|생성|시작)/i, /spawn\s*team/i, /create\s*team/i] },
    ],
  },
  {
    domain: 'observe',
    skill: '/wb-observe',
    patterns: [
      /현재\s*상태|진행\s*상황|상태\s*(확인|알려|보여)/i,
      /변경\s*(기록|로그)|로그\s*추가/i,
      /워크로그|작업\s*(로그|이력)/i,
      /분석|통계|사용\s*통계/i,
      /status|progress|analytics|stats|worklog|log/i,
    ],
    subcommands: [
      { sub: 'log', patterns: [/변경\s*(기록|로그)/i, /로그\s*추가/i, /기록\s*추가/i, /add\s*log/i, /record\s*change/i] },
      { sub: 'worklog search', patterns: [/워크로그\s*검색/i, /작업\s*(로그|이력)\s*검색/i, /search\s*worklog/i] },
      { sub: 'worklog', patterns: [/워크로그/i, /작업\s*(로그|이력)/i, /worklog/i, /work\s*log/i, /activity\s*log/i] },
      { sub: 'analytics', patterns: [/분석/i, /통계/i, /analytics/i, /usage\s*stats/i] },
    ],
  },
  {
    domain: 'infra',
    skill: '/wb-infra',
    patterns: [
      /콘솔|대시보드/i,
      /플레이그라운드|인터랙티브\s*(뷰|시각화)/i,
      /웹\s*(UI|으로|화면)/i,
      /console|dashboard|playground|interactive/i,
    ],
    subcommands: [
      { sub: 'console', patterns: [/콘솔/i, /대시보드/i, /웹\s*(UI|으로|화면)/i, /화면\s*(보여|열어|띄워)/i, /(open|start|launch|show)\s*(console|dashboard)/i, /web\s*(ui|dashboard|console)/i] },
      { sub: 'playground roadmap-explorer', patterns: [/로드맵\s*(플레이그라운드|시각화)/i, /마일스톤\s*시각화/i, /roadmap\s*(playground|visuali[sz]e)/i] },
      { sub: 'playground knowledge-map', patterns: [/지식\s*(맵|시각화)/i, /에센스\s*맵/i, /knowledge\s*map/i, /essence\s*map/i] },
      { sub: 'playground session-timeline', patterns: [/세션\s*(타임라인|시각화|히트맵)/i, /session\s*(timeline|heatmap|visuali[sz]e)/i] },
      { sub: 'playground team-composer', patterns: [/팀\s*(빌더|시각화|구성\s*플레이그라운드)/i, /team\s*(composer|builder|visuali[sz]e)/i] },
      { sub: 'playground', patterns: [/플레이그라운드/i, /인터랙티브\s*(뷰|시각화)/i, /(make|create|open)\s*playground/i, /interactive\s*view/i] },
    ],
  },
  {
    domain: 'admin',
    skill: '/wb-admin',
    patterns: [
      /워크북\s*(초기화|만들어|생성|셋업)/i,
      /텔레메트리|데이터\s*수집/i,
      /피드백|의견|버그\s*(신고|리포트)|기능\s*요청/i,
      /init\s*workbook|telemetry|feedback/i,
    ],
    subcommands: [
      { sub: 'init', patterns: [/워크북\s*(초기화|만들어|생성|셋업)/i, /init\s*workbook/i, /create\s*workbook/i] },
      { sub: 'telemetry', patterns: [/텔레메트리/i, /데이터\s*수집/i, /telemetry/i, /data\s*collection/i] },
      { sub: 'feedback', patterns: [/피드백/i, /의견\s*(제출|보내기)/i, /버그\s*(신고|리포트)/i, /기능\s*요청/i, /feedback/i, /report\s*bug/i] },
    ],
  },
  // 에이전트 (Task 도구로 호출)
  {
    domain: 'mission',
    skill: 'agent:wb-mission',
    patterns: [
      /미션\s*(루프|파이프라인|오케스트레이션)/i,
      /팀\s*미션|협업\s*미션|팀으로\s*미션/i,
      /mission\s*(loop|pipeline|orchestrat)/i,
      /team\s*mission|collaborative\s*mission/i,
    ],
    subcommands: [],
  },
  {
    domain: 'evolve',
    skill: 'agent:wb-evolve',
    patterns: [
      /(자동\s*)?진화/i,
      /규칙\s*제안/i,
      /인사이트\s*(생성|동기화)/i,
      /evolve/i,
      /evolution/i,
      /rule\s*suggest/i,
    ],
    subcommands: [],
  },
  {
    domain: 'worker',
    skill: 'agent:wb-worker',
    patterns: [
      /워커\s*(시작|실행|모드)?/i,
      /자율\s*실행/i,
      /(start|run)\s*worker/i,
      /worker\s*(mode|start)?/i,
    ],
    subcommands: [],
  },
  {
    domain: 'ship',
    skill: '/wb-ship',
    patterns: [
      /쉽|반영해줘|반영하고|커밋하고\s*(정리|업데이트|로드맵)/i,
      /ship\s*(it|this)?/i,
      /commit\s*and\s*(update|sync)/i,
    ],
    subcommands: [],
  },
  {
    domain: 'auto',
    skill: '/wb-auto',
    patterns: [
      /자동화\s*(상태|실행|설정)?/i,
      /신호|기준|후보\s*(풀|목록|선택)/i,
      /automation\s*(status|config)?/i,
      /signals?|criteria|candidates?/i,
    ],
    subcommands: [
      { sub: 'status', patterns: [/자동화\s*상태/i, /automation\s*status/i] },
      { sub: 'candidates', patterns: [/후보\s*(목록|풀|보여)/i, /candidates?/i] },
      { sub: 'config', patterns: [/자동화\s*(설정|활성화|비활성화)/i, /automation\s*config/i] },
      { sub: 'evolve', patterns: [/기준\s*(진화|분석|개선)/i, /criteria\s*evolve/i] },
    ],
  },
  {
    domain: 'cockpit',
    skill: '/wb-cockpit',
    patterns: [
      /콕피트|조종석/i,
      /전체\s*(현황|상태)|워크스페이스\s*(상태|대시보드|현황)/i,
      /한\s*눈에/i,
      /cockpit|workspace\s*(status|overview|dashboard)/i,
    ],
    subcommands: [],
  },
  {
    domain: 'scout',
    skill: '/wb-scout',
    patterns: [
      /스카우트|플러그인\s*(추천|찾기|검색|비교|탐색|점검|정리|포크)/i,
      /마켓플레이스/i,
      /scout|plugin\s*(recommend|find|compare|audit|cleanup|fork)/i,
      /marketplace/i,
    ],
    subcommands: [],
  },
  {
    domain: 'github',
    skill: '/wb-github',
    patterns: [
      /깃허브\s*(연동|동기화|상태|설정|푸시|풀)/i,
      /GitHub\s*(sync|status|config|push|pull)/i,
    ],
    subcommands: [],
  },
];

function detectIntent(input: string): IntentMatch | null {
  for (const domain of domainPatterns) {
    // 서브커맨드 먼저 검사 (더 구체적인 매칭 우선)
    for (const sub of domain.subcommands) {
      for (const pattern of sub.patterns) {
        if (pattern.test(input)) {
          return {
            intent: `${domain.domain}_${sub.sub.replace(/\s+/g, '_')}`,
            skill: domain.skill,
            subcommand: sub.sub,
            confidence: 0.9,
          };
        }
      }
    }
    // 도메인 레벨 매칭
    for (const pattern of domain.patterns) {
      if (pattern.test(input)) {
        return {
          intent: domain.domain,
          skill: domain.skill,
          confidence: 0.85,
        };
      }
    }
  }
  return null;
}
```

## 컨텍스트 인식

### 프로젝트 컨텍스트 추출

```typescript
function extractProjectContext(input: string): string | undefined {
  // "camfit 작업 시작" → project: "camfit"
  const projectMatch = input.match(/(\w+)\s*(작업|세션|프로젝트)/i);
  if (projectMatch) {
    return projectMatch[1];
  }
  return undefined;
}
```

### 인자 추출

```typescript
function extractArguments(intent: string, input: string): string | undefined {
  switch (intent) {
    case 'observe_log':
      // "버그 수정 로그 추가" → type: "fixed", desc: "버그 수정"
      const logMatch = input.match(/(추가|기록)\s*[:\-]?\s*(.+)/i);
      return logMatch?.[2];

    case 'session_checkpoint':
      // "타입 정의 완료 저장" → summary: "타입 정의 완료"
      const cpMatch = input.match(/(.+)\s*(저장|체크포인트)/i);
      return cpMatch?.[1];

    case 'plan_review':
      // "플랜 리뷰 ./my-plan.md" → 파일 경로 추출
      const prMatch = input.match(/(?:리뷰|검토|review)\s+(.+\.md)\b/i);
      return prMatch?.[1]?.trim();

    case 'plan_add':
      // "마일스톤 추가해줘 M5 새 기능" → "M5 새 기능"
      const msClean = input
        .replace(/마일스톤\s*(추가|만들어|생성|넣어)(해줘?|해주세요)?/gi, '')
        .replace(/(add|create|new)\s*milestone/gi, '')
        .trim();
      return msClean || undefined;

    case 'team_spawn':
      // "기능 구현할 팀 만들어줘" → 추출: "기능 구현할"
      const cleaned = input
        .replace(/팀\s*(만들어줘?|스폰|생성|시작)(해줘?|해주세요)?/gi, '')
        .replace(/(spawn|create)\s*team/gi, '')
        .trim();
      return cleaned || undefined;

    case 'plan_backlog_add':
      // "백로그에 추가해줘 Hook 시스템 -- 미구현" → "Hook 시스템 -- 미구현"
      const blAdd = input
        .replace(/백로그\s*(에\s*)?(추가|넣어|등록)(해줘?|해주세요)?/gi, '')
        .replace(/나중에\s*할\s*(일|것)\s*(추가|넣어)(해줘?|해주세요)?/gi, '')
        .replace(/add\s*(to\s*)?backlog/gi, '')
        .replace(/^[\s:]+/, '')
        .trim();
      return blAdd || undefined;

    case 'plan_backlog_remove':
      // "백로그에서 빼줘 Hook 시스템" → "Hook 시스템"
      const blRm = input
        .replace(/백로그\s*(에서\s*)?(제거|빼|삭제|지워)(줘?|주세요)?/gi, '')
        .replace(/(remove|delete)\s*(from\s*)?backlog/gi, '')
        .replace(/^[\s:]+/, '')
        .trim();
      return blRm || undefined;

    default:
      return undefined;
  }
}
```

## 출력 형식

### 인텐트 감지 시

스킬로 라우팅:
```
"작업 시작할게" → /wb-session 실행 중...
```

에이전트로 라우팅:
```
"미션 루프 시작" → agent wb-mission 호출 중...
```

### 확인이 필요한 경우

```
"camfit 작업" - 다음 중 어떤 것을 원하시나요?
  1. /wb-session camfit - camfit 프로젝트 세션 시작
  2. /wb-observe camfit - camfit 상태 확인
```

## 설정

워크북 preferences.yaml에서 인텐트 감지 설정:

```yaml
intent:
  enabled: true
  confirmBeforeExecute: false  # true면 실행 전 확인
  minConfidence: 0.8           # 최소 신뢰도
```

## 관련 스킬

- `workbook-discovery` - 워크북 자동 감지
- `wb-session` - 세션 생명주기 관리

## 관련 스킬/에이전트

모든 `/wb-*` 스킬 및 `wb-mission`, `wb-evolve`, `wb-worker` 에이전트

## Input

자연어 입력 (자동 감지). `$ARGUMENTS`로 직접 입력 가능.

## Examples

```
"워크북 뭐 할 수 있어?"
→ 전체 스킬/에이전트 목록 안내

"작업 시작할게"
→ /wb-session으로 라우팅

"에센스 추가하고 싶어"
→ /wb-knowledge add로 라우팅

"React 성능 최적화 해줘"
→ vercel-react-best-practices 번들 스킬로 위임

"마무리 해줘"
→ 복합 인텐트: /wb-ship → /wb-session end
```

---
> Source: [bocktae80/workbook-plugin](https://github.com/bocktae80/workbook-plugin) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-04 -->
