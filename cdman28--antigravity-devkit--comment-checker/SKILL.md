---
name: comment-checker
description: Post-processing tool that removes excessive AI-generated comments. Use after code generation to make it look human-written. Use when this capability is needed.
metadata:
  author: cdman28
---

# Comment Checker

## Role
AI가 생성한 코드에서 과도한 주석을 제거하는 Post-Processor.

## Problem

AI 생성 코드의 흔한 문제:

```python
# ❌ 과도한 주석
def calculate_total(items):
    # 총합을 저장할 변수 초기화
    total = 0
    # 각 아이템을 순회
    for item in items:
        # 가격 더하기
        total += item.price
    # 결과 반환
    return total
```

## Solution

`scripts/check-comments.py` 실행하여 자명한 주석 제거:

```python
# ✅ 정리 후
def calculate_total(items):
    total = 0
    for item in items:
        total += item.price
    return total
```

## Rules

### 보존할 주석

- **복잡한 알고리즘 설명** (Why)
- **비즈니스 로직 근거**
- **보안/성능 주의사항**
- **FIXME, TODO** (실제 작업 필요)

### 제거할 주석

- **코드와 동일 내용** ("변수 초기화")
- **함수명이 설명하는 내용** ("사용자 조회")
- **"이 함수는 ~합니다" 같은 번역체**
- **자명한 설명**

## Usage

코드 생성 완료 후:

1. `bash .agent/scripts/run-comment-check.sh`
2. 리포트 확인
3. 자동 수정 승인/거부

또는 직접 실행:

```bash
# 분석만 (dry-run)
python scripts/check-comments.py src/

# 자동 수정
python scripts/check-comments.py src/ --auto-fix

# 주석 비율 임계값 설정
python scripts/check-comments.py src/ --threshold 0.2
```

## Output

- `.agent/artifacts/comment-check-report.md` - 분석 결과 리포트

## Detection Patterns

자동으로 감지하는 자명한 주석 패턴:

- "변수 초기화", "Initialize variable"
- "함수 호출", "Call function"
- "반환", "Return"
- "이 함수는 ~합니다"
- "루프를 돌며", "Iterate through"

## Configuration

`.agent/rules/comment-rules.md`에서 추가 패턴 정의 가능:

```yaml
remove_patterns:
  - "변수 선언"
  - "값 할당"
  
preserve_patterns:
  - "HACK:"
  - "SECURITY:"
  - "PERFORMANCE:"
```

## When to Use

- **코드 생성 직후**: /build 워크플로우 완료 후
- **코드 리뷰 전**: Pull Request 제출 전
- **리팩토링 후**: 오래된 주석 정리

## Triggers

이 Skill은 다음 상황에서 자동으로 활성화됩니다:
- `/build` 워크플로우 완료 시
- "clean comments", "remove excessive comments" 요청 시
- "make code human-like" 요청 시

## Version
v1.0 (2026-01-24)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cdman28) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
