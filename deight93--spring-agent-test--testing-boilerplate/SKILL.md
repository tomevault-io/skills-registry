---
name: testing-boilerplate
description: Kotlin Spring Boot 테스트 보일러플레이트를 생성한다. 컨트롤러/서비스/리포지토리/통합 테스트 뼈대가 필요할 때 사용한다. Use when this capability is needed.
metadata:
  author: deight93
---

# 작업 절차
1. 테스트 유형(단위, 웹 슬라이스, 리포지토리 통합, 전체 통합)을 선택한다.
2. 최소 fixture와 Arrange-Act-Assert 구조를 만든다.
3. 성공/실패 경로를 모두 포함한다.
4. 반복되는 셋업이 생길 때만 helper/builder를 추가한다.

# 패턴
- 서비스: MockK로 협력 객체를 모킹하고 동작을 검증한다.
- 컨트롤러: HTTP 상태/응답 포맷/검증 오류를 확인한다.
- 리포지토리: 매핑/쿼리 동작을 테스트 DB 기준으로 확인한다.

# 품질 기준
- 테스트 이름은 동작 중심으로 작성한다.
- 관찰 가능한 결과를 검증한다.
- private 구현 상세 검증은 피한다.

# 번들 리소스
- 레이어 선택 가이드: `references/test-layer-selection.md`
- 프로젝트 컨트롤러 테스트 가이드: `../../../agents/guidelines/controller-testing.md`
- 프로젝트 서비스 테스트 가이드: `../../../agents/guidelines/service-testing.md`
- 프로젝트 리포지토리 테스트 가이드: `../../../agents/guidelines/repository-testing.md`
- 프로젝트 JSON 모델 테스트 가이드: `../../../agents/guidelines/json-model-testing.md`
- 테스트 뼈대 생성 스크립트: `scripts/create_test_skeleton.py`
- 테스트 메서드 예시: `assets/test_method_examples.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/deight93) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
