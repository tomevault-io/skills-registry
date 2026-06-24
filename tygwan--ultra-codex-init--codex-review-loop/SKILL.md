---
name: codex-review-loop
description: Orchestrates a dual-AI engineering loop where Codex plans and implements, while Codex validates and reviews, with continuous feedback for optimal code quality. "듀얼 AI", "코덱스 루프", "교차 검증", "dual AI", "cross review", "codex loop" 키워드에 반응. Use when this capability is needed.
metadata:
  author: tygwan
---

# Codex Review Loop

Codex가 설계/구현하고, Codex가 검증/리뷰하는 듀얼 AI 엔지니어링 루프.

## Available Models

| Model | Description | Use Case |
|-------|-------------|----------|
| `gpt-5.2-codex` | 최신 에이전틱 코딩 모델 (기본값) | Plan 검증, 코드 리뷰 |
| `gpt-5.1-codex-mini` | 경량 비용 효율 모델 | 빠른 검증, 간단한 리뷰 |
| `gpt-5.1-codex-max` | 확장 에이전틱 코딩 모델 | 대규모 아키텍처 리뷰 |

## Core Loop

```
Plan (Codex) → Validate (Codex) → Feedback →
Implement (Codex) → Review (Codex) →
Fix (Codex) → Re-validate (Codex) → Repeat until perfect
```

## Phases

| Phase | Actor | Action |
|-------|-------|--------|
| 1. Planning | Codex | Detailed plan, step breakdown, assumptions |
| 2. Plan Validation | Codex | Logic errors, edge cases, architecture, security |
| 3. Feedback Loop | Both | Refine plan based on Codex feedback, re-validate |
| 4. Execution | Codex | Implement with Edit/Write/Read tools |
| 5. Cross-Review | Codex | Bug detection, performance, best practices |
| 6. Iteration | Both | Fix → re-validate → repeat until quality met |

### Phase 2: Codex Validation Command

```bash
echo "Review this implementation plan and identify any issues:
[plan]
Check for: Logic errors, Missing edge cases, Architecture flaws, Security concerns" \
| codex exec -m <model> --config model_reasoning_effort="<level>" --sandbox read-only
```

ask the user first: model (`gpt-5.2-codex` (Recommended) / `gpt-5.1-codex-mini` / `gpt-5.1-codex-max`) + reasoning (`low`/`medium`/`high`)

### Phase 6: Resume for Iteration

```bash
echo "Review the updated implementation" | codex exec resume --last
```

> Resume inherits all settings from original session.

## Command Reference

| Phase | Command | Purpose |
|-------|---------|---------|
| Validate plan | `echo "plan" \| codex exec --sandbox read-only` | Logic check before coding |
| Implement | Codex Edit/Write/Read tools | Execute validated plan |
| Review code | `echo "review" \| codex exec --sandbox read-only` | Validate implementation |
| Continue | `echo "next" \| codex exec resume --last` | Continue session |
| Re-validate | `echo "verify" \| codex exec resume --last` | Re-check after fixes |

## Recovery

| Situation | Action |
|-----------|--------|
| Codex finds problems | Codex analyzes → fixes → re-sends to Codex |
| Implementation errors | Codex adjusts strategy → re-validates with Codex |
| Architectural changes needed | ask the user before proceeding |
| Multiple files affected | Confirm approach with user |

## Best Practices

| # | Practice |
|---|----------|
| 1 | Always validate plans before execution |
| 2 | Never skip cross-review after changes |
| 3 | Maintain clear handoff between AIs |
| 4 | Document who did what for context |
| 5 | Use resume to preserve session state |

## Error Handling

| # | Rule |
|---|------|
| 1 | Stop on non-zero exit from Codex |
| 2 | Summarize feedback → ask the user for direction |
| 3 | Confirm with user before significant/breaking changes |
| 4 | Evaluate warning severity before deciding next steps |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tygwan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
