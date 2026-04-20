---
name: documentation
description: 기술 문서 작성 가이드, 문서 유형별 템플릿 Use when this capability is needed.
metadata:
  author: jaeyeonling
---

# Documentation Guide

## 문서 유형

### README.md

프로젝트의 첫인상. 필수 정보를 빠르게 전달.

```markdown
# Project Name

프로젝트 한 줄 설명

## Features

- 주요 기능 1
- 주요 기능 2

## Quick Start

### Prerequisites

- 필요한 런타임/도구 목록

### Installation

\`\`\`bash
[설치 명령어]
\`\`\`

### Usage

\`\`\`
[사용 예시]
\`\`\`

## Documentation

자세한 문서는 [여기](./docs)를 참조하세요.

## Contributing

기여 가이드는 [CONTRIBUTING.md](./CONTRIBUTING.md)를 참조하세요.

## License

MIT
```

### API 문서

```markdown
## `functionName(param1, param2)`

함수 설명

### Parameters

| Name | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| param1 | `string` | Yes | - | 파라미터 설명 |
| param2 | `number` | No | `10` | 파라미터 설명 |

### Returns

`Result` - 반환값 설명

### Throws

- `ValidationError` - param1이 비어있을 때
- `NetworkError` - API 호출 실패 시

### Example

\`\`\`
result = functionName("value", 20)
print(result)
// Output: { success: true, data: ... }
\`\`\`

### Notes

- 특별한 주의사항
- 성능 관련 정보
```

### CHANGELOG.md

```markdown
# Changelog

All notable changes to this project will be documented in this file.

## [Unreleased]

### Added
- 새로운 기능

### Changed
- 변경된 기능

### Deprecated
- 곧 제거될 기능

### Removed
- 제거된 기능

### Fixed
- 버그 수정

### Security
- 보안 관련 수정

## [1.0.0] - YYYY-MM-DD

### Added
- Initial release
- Feature A
- Feature B
```

### CONTRIBUTING.md

```markdown
# Contributing Guide

프로젝트에 기여해주셔서 감사합니다!

## How to Contribute

1. 이슈 확인 또는 생성
2. Fork 후 브랜치 생성
3. 변경사항 구현
4. 테스트 작성 및 실행
5. PR 생성

## Development Setup

\`\`\`bash
git clone https://github.com/user/project
cd project
[설치 명령어]
[테스트 명령어]
\`\`\`

## Code Style

- 프로젝트 코드 스타일 가이드 준수
- 포맷터 사용

## Commit Messages

Conventional Commits 형식을 따릅니다.

## Pull Request Process

1. 테스트 통과 확인
2. 문서 업데이트 (필요시)
3. 리뷰어 지정
4. 승인 후 머지
```

## 코드 주석 가이드

### 좋은 주석

```text
# WHY를 설명하는 주석
# 비즈니스 규칙: 30일 후 자동 만료
EXPIRY_DAYS = 30

# 복잡한 비즈니스 로직 설명:
# - 트라이얼 기간은 구독으로 취급
# - 취소된 구독도 기간 종료 전까지 활성
function calculateSubscriptionStatus(user):
    ...
```

### 피해야 할 주석

```text
# Bad: 코드가 이미 설명하는 내용
# i를 1 증가
i = i + 1

# Bad: 오래된 주석
# TODO: 나중에 수정 (2019년...)

# Bad: 주석 처리된 코드
# function oldImplementation(): ...
```

## 문서 주석 (Docstring)

```text
# 주문 총액을 계산합니다.
#
# @param items - 주문 항목 배열
# @param discount - 할인율 (0-1 사이)
# @returns 할인이 적용된 총액
#
# @example
#   total = calculateOrderTotal(items, 0.1)
#
# @throws ValidationError items가 비어있을 때
# @since 1.2.0
function calculateOrderTotal(items, discount = 0):
    ...
```

## 문서 작성 원칙

### 독자 중심

1. 독자가 누구인가?
2. 무엇을 알고 싶어하는가?
3. 어떤 배경 지식이 있는가?

### KISS (Keep It Simple, Stupid)

- 짧은 문장
- 간단한 단어
- 불필요한 전문 용어 제거

### 구조화

- 제목과 소제목 활용
- 목록으로 정보 정리
- 코드 예시 포함

### 유지보수

- 코드 변경 시 문서도 업데이트
- 자동화 가능한 부분은 자동화
- 정기적인 문서 리뷰

## 문서 작성 체크리스트

### README.md
- [ ] 프로젝트 설명이 한 줄로 명확한가?
- [ ] Quick Start가 실제로 동작하는가?
- [ ] 필수 정보(설치, 사용법)가 포함되었는가?
- [ ] 라이선스가 명시되었는가?

### API 문서
- [ ] 모든 파라미터가 설명되었는가?
- [ ] 반환값과 타입이 명시되었는가?
- [ ] 예외/에러 케이스가 문서화되었는가?
- [ ] 실행 가능한 예시가 포함되었는가?

### 코드 주석
- [ ] "왜(Why)"를 설명하는가?
- [ ] 복잡한 비즈니스 로직이 설명되었는가?
- [ ] 주석이 코드와 일치하는가?
- [ ] 불필요한 주석이 제거되었는가?

### 일반
- [ ] 독자 수준에 맞는가?
- [ ] 전문 용어가 설명되었는가?
- [ ] 문서가 최신 상태인가?

## 관련 스킬

- `api-design`: API 문서화
- `git-workflow`: CHANGELOG 작성

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jaeyeonling) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
