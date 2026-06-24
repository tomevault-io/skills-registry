---
name: agent-eval
description: 커스텀 작업에서 Claude Code, Aider, Codex 등 코딩 에이전트를 정면 비교하고 pass rate, cost, time, consistency 지표를 측정합니다. Use when this capability is needed.
metadata:
  author: SAM42-Lab
---

# Agent Eval Skill

재현 가능한 작업에서 코딩 에이전트를 정면 비교하는 경량 CLI 도구입니다. "어떤 코딩 에이전트가 제일 좋은가?" 같은 비교를 감으로 하지 않고 체계화합니다.

## 활성화 시점

- 내 코드베이스에서 코딩 에이전트(Claude Code, Aider, Codex 등)를 비교할 때
- 새 도구나 모델 도입 전에 에이전트 성능을 측정할 때
- 에이전트의 모델 또는 도구 업데이트 후 회귀 점검을 할 때
- 팀 차원의 데이터 기반 에이전트 선택 결정을 만들 때

## 설치

> **주의:** `agent-eval`은 반드시 저장소 소스를 검토한 뒤 설치합니다.

## 핵심 개념

### YAML 작업 정의

작업은 선언적으로 정의합니다. 각 작업은 무엇을 할지, 어떤 파일을 건드릴지, 성공을 어떻게 판정할지를 명시합니다.

```yaml
name: add-retry-logic
description: Add exponential backoff retry to the HTTP client
repo: ./my-project
files:
  - src/http_client.py
prompt: |
  Add retry logic with exponential backoff to all HTTP requests.
  Max 3 retries. Initial delay 1s, max delay 30s.
judge:
  - type: pytest
    command: pytest tests/test_http_client.py -v
  - type: grep
    pattern: "exponential_backoff|retry"
    files: src/http_client.py
commit: "abc1234"
```

### Git Worktree 격리

각 에이전트 실행은 전용 git worktree를 받습니다. Docker 없이도 재현성 있는 격리가 가능하며, 에이전트끼리 서로 간섭하거나 기본 저장소를 오염시키지 못하게 합니다.

### 수집 지표

| Metric | What It Measures |
|--------|-----------------|
| Pass rate | 판정 기준을 통과하는 코드를 만들었는가 |
| Cost | 작업당 API 비용 |
| Time | 완료까지 걸린 실제 시간 |
| Consistency | 반복 실행 간 통과율 |

## 워크플로

### 1. 작업 정의

작업당 YAML 파일 하나씩 `tasks/` 디렉터리를 만듭니다.

```bash
mkdir tasks
```

### 2. 에이전트 실행

```bash
agent-eval run --task tasks/add-retry-logic.yaml --agent claude-code --agent aider --runs 3
```

각 실행은 다음을 수행합니다.

1. 지정 커밋에서 새 git worktree 생성
2. 프롬프트를 에이전트에 전달
3. judge 기준 실행
4. pass/fail, cost, time 기록

### 3. 결과 비교

```bash
agent-eval report --format table
```

```text
Task: add-retry-logic (3 runs each)
┌──────────────┬───────────┬────────┬────────┬─────────────┐
│ Agent        │ Pass Rate │ Cost   │ Time   │ Consistency │
├──────────────┼───────────┼────────┼────────┼─────────────┤
│ claude-code  │ 3/3       │ $0.12  │ 45s    │ 100%        │
│ aider        │ 2/3       │ $0.08  │ 38s    │  67%        │
└──────────────┴───────────┴────────┴────────┴─────────────┘
```

## Judge 유형

### 코드 기반(결정론적)

```yaml
judge:
  - type: pytest
    command: pytest tests/ -v
  - type: command
    command: npm run build
```

### 패턴 기반

```yaml
judge:
  - type: grep
    pattern: "class.*Retry"
    files: src/**/*.py
```

### 모델 기반(LLM-as-judge)

```yaml
judge:
  - type: llm
    prompt: |
      Does this implementation correctly handle exponential backoff?
      Check for: max retries, increasing delays, jitter.
```

## 모범 사례

- 장난감 예제가 아니라 실제 워크로드를 대표하는 3~5개 작업부터 시작합니다.
- 에이전트는 비결정적이므로 최소 3회 이상 실행해 분산을 봅니다.
- 결과 재현성을 위해 YAML에 커밋을 고정합니다.
- 작업마다 테스트나 빌드 같은 결정론적 judge를 최소 하나 포함합니다.
- 통과율과 함께 비용도 추적합니다.
- 작업 정의도 테스트 fixture처럼 코드로 버전 관리합니다.

## 링크

- Repository: [github.com/joaquinhuigomez/agent-eval](https://github.com/joaquinhuigomez/agent-eval)

---
> Source: [SAM42-Lab/everything-claude-code-kr](https://github.com/SAM42-Lab/everything-claude-code-kr) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
