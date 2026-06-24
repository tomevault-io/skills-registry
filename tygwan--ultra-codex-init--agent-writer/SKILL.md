---
name: agent-writer
description: Codex Agent 작성 전문가. Frontmatter 검증, 트리거 키워드 제안, 구조 템플릿 제공, 테스트 시나리오 작성을 지원합니다. Use when this capability is needed.
metadata:
  author: tygwan
---

# Agent-writer Skill

Migrated from the legacy agent profile. Use this as an on-demand specialist workflow.


# Agent Writer

## Purpose

> Codex Agent를 효과적으로 작성하고 검증하는 전문가

## When to Use

- 새 에이전트 생성 시
- 기존 에이전트 구조 개선 시
- Frontmatter 검증 필요 시
- 트리거 키워드 최적화 시

## Agent Structure Template

```markdown
name: {agent-name}
description: {한 줄 설명}
triggers:
  ko: ["키워드1", "키워드2", ...]
  en: ["keyword1", "keyword2", ...]
integrates_with: ["other-agent1", "other-agent2"]
outputs: ["output/path1", "output/path2"]
tools: [Read, Write, Bash, Grep, Glob]
model: haiku|sonnet|opus

# {Agent Name}

## Purpose
> 1-2문장 핵심 역할

## When to Use
- 사용자가 X를 요청할 때
- Y 상황에서

## Integration
```
┌─────────────┐     ┌─────────────┐
│  this agent │────▶│ other agent │
└─────────────┘     └─────────────┘
```

## Core Workflow

1. **Step 1**: 설명
2. **Step 2**: 설명
3. **Step 3**: 설명

## Output Format

```markdown
예시 출력
```

## Examples

**Input**: "사용자 요청 예시"
**Output**: [결과 설명]

## Best Practices

1. Practice 1
2. Practice 2
```

## Frontmatter Schema

### Required Fields

| Field | Type | Description |
|-------|------|-------------|
| `name` | string | 에이전트 고유 식별자 (kebab-case) |
| `description` | string | 에이전트 역할 설명 + 트리거 키워드 |
| `tools` | array | 사용 가능한 도구 목록 |

### Optional Fields

| Field | Type | Description |
|-------|------|-------------|
| `triggers` | object | 한국어/영어 트리거 키워드 |
| `integrates_with` | array | 연동 에이전트 목록 |
| `outputs` | array | 생성/수정하는 파일 경로 |
| `model` | string | 권장 모델 (haiku/sonnet/opus) |

### Model Selection Guide

| Model | Use Case | Cost |
|-------|----------|------|
| `haiku` | 간단한 작업, 빠른 응답, 파일 읽기/쓰기 | 💰 |
| `sonnet` | 복잡한 분석, 코드 생성, 기본값 | 💰💰 |
| `opus` | 고급 추론, 아키텍처 설계 | 💰💰💰 |

## Trigger Keywords Best Practices

### 다양한 표현 커버
```yaml
triggers:
  ko:
    - "진행상황"      # 명사형
    - "진행 상황"     # 띄어쓰기 변형
    - "어디까지"      # 구어체
    - "얼마나 됐"     # 질문형
    - "현황"          # 동의어
  en:
    - "progress"
    - "status"
    - "how far"
    - "completion"
```

### 카테고리별 그룹화
```yaml
triggers:
  ko:
    # 상태 확인
    - "진행상황"
    - "현재 상태"
    # 완료 관련
    - "완료율"
    - "몇 퍼센트"
    # 남은 작업
    - "뭐 남았"
    - "남은 작업"
```

## Validation Checklist

에이전트 검증 시 확인할 항목:

| Check | Question |
|:-----:|----------|
| ⬜ | name이 kebab-case? |
| ⬜ | description에 역할이 명확? |
| ⬜ | triggers가 다양한 표현 커버? |
| ⬜ | tools가 최소한으로 지정? |
| ⬜ | model이 작업 복잡도에 적합? |
| ⬜ | Integration 다이어그램 포함? |
| ⬜ | Output Format 예시 포함? |
| ⬜ | Examples 섹션 포함? |

## Commands

### Generate Agent
```
"에이전트 만들어줘: {역할 설명}"
→ 역할 분석 → 템플릿 생성 → 키워드 제안
```

### Validate Agent
```
"에이전트 검증해줘: {파일 경로}"
→ Frontmatter 파싱 → 체크리스트 검증 → 개선 제안
```

### Suggest Triggers
```
"트리거 키워드 제안해줘: {에이전트명}"
→ 역할 분석 → 다양한 표현 생성
```

### Generate Test Scenarios
```
"테스트 시나리오 작성해줘: {에이전트명}"
→ 입력/출력 예시 생성
```

## Integration Map

```
agent-writer
     │
     ├──▶ config-validator (설정 검증)
     │
     ├──▶ subagent-creator skill (연동)
     │
     └──▶ test-helper (테스트 시나리오)
```

## Common Patterns

### File Processing Agent
```yaml
name: file-processor
tools: [Read, Write, Glob]
model: haiku
```

### Analysis Agent
```yaml
name: code-analyzer
tools: [Read, Grep, Glob]
model: sonnet
```

### Automation Agent
```yaml
name: workflow-automator
tools: [Read, Write, Bash, Grep, Glob]
model: sonnet
```

### Research Agent
```yaml
name: web-researcher
tools: [WebSearch, WebFetch, Read]
model: sonnet
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tygwan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
