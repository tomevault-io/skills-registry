---
name: raven-test
description: Tester Agent의 심층 QA 프로세스. 커버리지 분석, Edge Case 검증, 심층 테스트, 리포트 생성의 단계를 정의합니다. Use when this capability is needed.
metadata:
  author: slowth-kim
---

# Raven Test - Deep QA Process

🧪 Tester Agent의 심층 QA 프로세스입니다.

> **Note**: 기본 테스트와 PRD 검증은 Coding Agent (`raven-code`)에 통합되어 있습니다. 이 skill은 심층 QA가 필요한 경우에 사용합니다.

## Main Menu

```
🧪 Tester Agent - Deep QA

테스트 프레임워크: {detected}
마지막 커버리지: {coverage}%

[1] deep-verify - 심층 검증 (Edge Cases)
[2] coverage    - 커버리지 분석 및 개선
[3] audit       - 코드 품질 감사
[4] report      - 테스트 리포트 생성
[x] 종료
```

---

## deep-verify - 심층 검증

Coding Agent의 자체 검증 이후 추가적인 심층 검증:

### 1. 컨텍스트 로드
- `working.json` 확인 → 최근 구현 내용 파악
- 관련 PRD 로드
- 변경된 파일 목록 확인

### 2. Edge Case 분석
```
Edge Case 검증 항목:
□ 경계값 테스트
□ null/undefined 처리
□ 빈 입력 처리
□ 대용량 데이터
□ 동시성/레이스 컨디션
□ 에러 복구
```

### 3. 심층 테스트 실행
각 Edge Case에 대해:
```
"{edge_case}"
[t] 테스트 실행
[m] 수동 검증
[s] Skip
```

### 4. 발견 사항
```
심층 검증 결과:
✅ Verified: {pass_count}
⚠️ Warning: {warn_count}
❌ Issue: {issue_count}
```

### 5. 이슈 보고
이슈 발견 시:
```
발견된 이슈:
1. {issue_description}
   재현: {steps}
   심각도: {high/medium/low}

[c] Coding Agent에게 전달
[i] 무시
```

---

## audit - 코드 품질 감사

### 1. 분석 범위
```
감사 범위:
[r] 최근 변경된 파일
[a] 전체 프로젝트
[f] 특정 폴더
```

### 2. 품질 체크리스트
- 코드 복잡도 (Cyclomatic Complexity)
- 중복 코드
- 보안 취약점 패턴
- 성능 이슈 패턴
- 테스트 누락 영역

### 3. 권고사항
```
품질 감사 결과:

개선 필요:
- {file}: {recommendation}

권장 사항:
- {suggestion}
```

---

## test - 테스트 실행

### 1. 테스트 감지
```bash
# Node.js
grep -E "(jest|mocha|vitest)" package.json

# Python
ls pytest.ini setup.py

# Go
ls go.mod

# Rust
ls Cargo.toml
```

### 2. 테스트 실행
```
테스트 범위:
[a] 전체 (all)
[f] 특정 파일/폴더
[w] Watch 모드
```

### 3. 결과
```
테스트 결과:
- Total: {total}
- Passed: {passed}
- Failed: {failed}
- Duration: {time}
```

실패 있으면:
```
실패한 테스트:
- {test_name}: {error_message}

분석할까요? [y/n]
```

---

## coverage - 커버리지 확인

### 1. 커버리지 실행
프레임워크별 커버리지 도구 감지 및 실행

### 2. 리포트
```
커버리지 결과:
- Lines: {line_coverage}%
- Branches: {branch_coverage}%
- Functions: {function_coverage}%
```

커버리지 낮은 파일 목록

### 3. 권장사항
- 더 많은 테스트가 필요한 영역 제안
- 커버리지 없는 중요 경로 식별

---

## report - 테스트 리포트

### 1. 범위
```
리포트 범위:
[t] 특정 task
[p] 전체 프로젝트
```

### 2. 리포트 생성
```markdown
# Verification Report

## Task: {task_id}
## Date: {timestamp}

### Acceptance Criteria
| # | Criteria | Status | Notes |
|---|----------|--------|-------|

### Automated Tests
- Framework: {framework}
- Total/Passed/Failed

### Edge Cases Checked

### Overall Result
{PASS/FAIL}

### Recommendations
```

### 3. 출력
```
[s] 화면에 표시
[f] 파일로 저장 (.raven/reports/)
```

---

## Test Framework Commands

| Framework | Command |
|-----------|---------|
| Jest | `npm test` |
| Pytest | `pytest` |
| Go | `go test ./...` |
| Cargo | `cargo test` |
| Vitest | `npx vitest run` |

---

## BMAD Integration

- **시작**: `belief-load`, `working.json` 확인
- **분석 후**: `dialogue-save` (분석 결과 요약)
- **이슈 발견 시**: `handoff-write` (이슈 상세 포함) → Coding Agent
- **리포트 생성 시**: `.raven/reports/`에 저장

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/slowth-kim) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
