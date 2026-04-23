---
name: linter-setting
description: | Use when this capability is needed.
metadata:
  author: guny524
---

# Linter Setting

프로젝트에 린터를 설정할 때 이 skill 활성화.

## 지원 언어별 도구

| 기능 | TypeScript | Go | Python | Protobuf |
|------|------------|-----|--------|----------|
| 메인 린터 | typescript-eslint | golangci-lint | ruff | buf |
| Import Order | import/order | gci | I (isort) | - |
| Cognitive Complexity | sonarjs | gocognit | flake8-cognitive-complexity | - |
| Cyclomatic Complexity | - | gocyclo | C90 (mccabe) | - |
| Switch Exhaustive | switch-exhaustiveness-check | exhaustive | - | - |
| Magic Numbers | no-magic-numbers | mnd | - | - |
| Dead Code | knip | unused, staticcheck | vulture | - |
| Code Duplication | jscpd | dupl | - | - |
| Function Length | max-lines-per-function | funlen | PLR0915 | - |
| Const Extraction | - | goconst | - | - |
| Unnecessary Conversion | - | unconvert | - | - |
| Unused Parameters | - | unparam | - | - |
| 의존성 규칙 | dependency-cruiser | go-arch-lint | import-linter | - |
| Style Checker | - | - | - | protolint |
| 코드 통계 | tokei | tokei | tokei | - |
| Test Framework | vitest | go test | pytest | - |
| Test Coverage | @vitest/coverage-v8 (Branch) | gobco (Condition) | pytest-cov (Branch) | - |

## Lint Ignore 규칙 (모든 린터 공통)

**원칙: 외부에서 다운로드하거나 코드 생성 도구로 자동 생성된 것만 ignore. 직접 작성한 코드는 전부 lint 대상.**

### Ignore 가능

| 유형 | 예시 | 확인 방법 |
|------|------|-----------|
| 외부 UI 라이브러리 | shadcn (`components/ui/`) | npx 등으로 다운로드 |
| 코드 생성 도구 산출물 | protobuf, OpenAPI, GraphQL codegen | Makefile의 `generate`/`copy`/`clean` 타겟 |
| 외부 라이브러리 타입 선언 | node_modules에서 복사된 `*.d.ts` | 직접 작성하지 않은 것 |
| 빌드/의존성 산출물 | `node_modules/`, `.next/`, `dist/` | 빌드 도구/패키지 매니저 생성 |

### Ignore 불가

| 유형 | 예시 |
|------|------|
| 직접 작성한 모든 코드 | 유틸리티, 모듈, 비즈니스 로직 |
| 직접 작성한 타입 선언 | 프로젝트용 `*.d.ts` |

### 새 ignore 추가 전 확인

1. "이 파일이 외부에서 왔거나 자동 생성되는가?"
2. Makefile의 `generate`/`copy`/`clean` 타겟에 포함되는가?
3. **아니면 ignore 추가 금지** → unused export는 실제로 제거하거나 사용처 추가

## 테스트 파일 예외 규칙

테스트 코드에서 허용되는 패턴 (ruff `per-file-ignores` 사용):
- **PLR2004**: `assert result == 42` 매직 넘버 직접 비교
- **E712**: `assert x == True` True/False 비교
- **RUF043**: `pytest.raises(match="pattern.*")` regex 메타문자

Python 설정 예시는 [references/python.md](references/python.md) 참조.

## 매개변수 순서 가이드라인

린터로 강제 불가. 코드 리뷰 시 확인:

- **Main Feature → Sub Feature 순**: 핵심 기능 관련 매개변수를 먼저 배치
- 예시: `storage` (Optuna 결과 저장) → `artifact` (부가 결과) → `shared_cache` (캐시)
- 관련 기능 내에서도 중요도 순: `dir` → `mem_use` (bool) → `size`

## Makefile 표준 타겟

프로젝트별로 아래 타겟 중 필요한 것만 사용. **새 타겟 추가 금지** - 기존 타겟에 기능 통합.

| 타겟 | 용도 | 비고 |
|------|------|------|
| `install` | 의존성 설치 | 선택적 |
| `generate` | 코드 생성 (protobuf, codegen 등) | 선택적 |
| `lint` | 린터 실행 (모든 린터 한번에) | 필수 |
| `test` | 테스트 실행 | 필수 |
| `test-cover` | 테스트 + coverage threshold 체크 + 상세 리포트 | 권장 |
| `build` | 빌드 | 필수 |
| `clean` | 빌드 산출물 정리 | 선택적 |

**원칙** (lint/test 타겟 한정):
- 하나의 타겟에 관련 기능 모두 통합 (예: `make lint`에 모든 린터, `make test-cover`에 테스트+리포트+threshold)
- lint/test 관련 별도 타겟(`lint-report`, `test-html` 등) 만들지 않음
- 다른 용도 타겟(`push`, `image-build`, `deploy` 등)은 프로젝트 필요에 따라 자유롭게 추가

## 설정 절차

1. 언어 확인 → 해당 reference 파일 읽기
2. 패키지/도구 설치
3. 설정 파일 생성
4. Makefile 타겟 구성 (위 표준 타겟 준수)
5. 동작 확인

## References

- TypeScript: [references/typescript.md](references/typescript.md)
- Go: [references/go.md](references/go.md)
- Python: [references/python.md](references/python.md)
- Protobuf: [references/protobuf.md](references/protobuf.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/guny524) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
