---
name: spring-controller
description: Spring REST 컨트롤러와 DTO를 생성/수정한다. 신규 API 라우트, 요청 검증, 응답 매핑, 컨트롤러 예외 처리 구현 시 사용한다. Use when this capability is needed.
metadata:
  author: deight93
---

# 작업 절차
1. feature 이름, 엔드포인트 경로, HTTP 메서드를 확정한다.
2. 검증 애노테이션이 포함된 불변 요청/응답 DTO를 정의한다.
3. 컨트롤러 메서드는 서비스 호출 위주로 얇게 유지한다.
4. 도메인/애플리케이션 결과를 API 응답 모델로 매핑한다.
5. 성공/검증 실패/대표 오류 케이스에 대한 컨트롤러 테스트를 추가한다.

# 규칙
- 기본 경로는 `/api/v1/<resource>`를 사용한다.
- 비즈니스 로직은 서비스 계층에 둔다.
- 응답/에러 포맷을 일관되게 유지한다.

# 모듈 내부 배치 규칙
- 컨트롤러 관련 코드는 `modules/api`에만 둔다.
- 권장 패키지: `controller`, `dto/request`, `dto/response`, `exception`, `config/security`
- `service`, `entity`, `repository` 구현은 이 스킬 범위에서 직접 생성하지 않는다(각 전용 스킬 사용).

# 완료 체크리스트
- OpenAPI 애노테이션/문서가 갱신되었다.
- `@WebMvcTest`에 핵심 실패 케이스가 포함되었다.

# 번들 리소스
- 레퍼런스 인덱스: `references/INDEX.md`
- 구현 체크리스트: `references/controller-implementation-checklist.md`
- 컨트롤러 코드 샘플: `references/rest-controller-sample.md`
- 컨트롤러 테스트 샘플: `references/controller-test-sample.md`
- 프로젝트 백엔드 아키텍처 가이드: `../../../agents/guidelines/backend-architecture.md`
- 프로젝트 컨트롤러 테스트 가이드: `../../../agents/guidelines/controller-testing.md`
- 프로젝트 페이지네이션 가이드: `../../../agents/guidelines/pagination.md`
- 프로젝트 JSON 모델 가이드: `../../../agents/guidelines/json-model-testing.md`
- 컨트롤러 스캐폴드 스크립트: `scripts/scaffold_controller.py`
- 컨트롤러 템플릿: `assets/controller_template.kt`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/deight93) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
