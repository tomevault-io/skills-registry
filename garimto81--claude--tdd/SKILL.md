---
name: tdd
description: Guide Test-Driven Development with Red-Green-Refactor discipline Use when this capability is needed.
metadata:
  author: garimto81
---

# /tdd - Test-Driven Development

## OMC Integration

이 스킬은 OMC `tdd` 스킬에 위임합니다.

### 실행 방법

```python
Skill(skill="oh-my-claudecode:tdd", args="feature-name")

# 또는 직접 에이전트 호출
Task(subagent_type="oh-my-claudecode:tdd-guide", model="sonnet",
     prompt="TDD 워크플로우 실행: [기능 설명]")
```

### OMC 에이전트

| 에이전트 | 모델 | 용도 |
|----------|------|------|
| `tdd-guide` | sonnet | 표준 TDD 워크플로우 |
| `tdd-guide-low` | haiku | 간단한 테스트 제안 |

## 인과관계 (CRITICAL - 절대 보존)

```
/auto Tier 5 AUTONOMOUS
    └── /tdd <feature> (테스트 없는 코드 감지 시)

/work --loop Tier 3
    └── /tdd <feature> (새 기능 구현 요청 시)
```

**이 인과관계는 OMC 위임과 무관하게 그대로 유지됩니다.**

## Red-Green-Refactor Cycle

### 🔴 Red: 실패하는 테스트 작성

```bash
# 테스트 파일 먼저 작성
pytest tests/test_feature.py -v
# ❌ FAILED - 예상된 동작

git commit -m "test: Add feature test (RED) 🔴"
```

### 🟢 Green: 최소 구현

```bash
# 테스트 통과하는 최소 코드
pytest tests/test_feature.py -v
# ✅ PASSED

git commit -m "feat: Implement feature (GREEN) 🟢"
```

### ♻️ Refactor: 코드 개선

```bash
# 테스트 유지하며 개선
pytest tests/test_feature.py -v
# ✅ PASSED (유지)

git commit -m "refactor: Improve feature ♻️"
```

## 사용법

```bash
/tdd <feature-name>

# 예시
/tdd user-authentication
/tdd payment-processing
```

## 커맨드 파일 참조

상세 워크플로우: `.claude/commands/tdd.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/garimto81) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
