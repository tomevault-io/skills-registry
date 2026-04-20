---
name: analyze-codebase
description: 코드베이스 구조를 분석하고 요약합니다 Use when this capability is needed.
metadata:
  author: kenshin579
---

$ARGUMENTS 경로의 코드베이스를 분석하세요.

## 분석 항목
1. **디렉토리 구조**: 파일과 패키지 구성
2. **핵심 타입/인터페이스**: 주요 struct와 interface 목록
3. **의존성**: 외부 라이브러리와 내부 패키지 의존 관계
4. **테스트 현황**: 테스트 파일 유무, 테스트 패턴 (testify, testcontainers 등)
5. **진입점**: main.go 또는 주요 실행 파일

## 출력 형식
마크다운으로 요약하되, 각 파일은 `파일명:라인번호` 형식으로 참조

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kenshin579) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
