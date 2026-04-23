---
name: code-reviewer
description: 보안 스캔, 품질 지표 및 모범 사례 분석을 포함한 자동화된 코드 리뷰입니다. 다음을 위한 코드 리뷰 시 사용합니다: (1) 보안 취약점 및 일반적인 공격 벡터, (2) 코드 품질 이슈 및 유지보수 문제, (3) 성능 병목 현상 및 최적화 기회, (4) 모범 사례 및 디자인 패턴, (5) 테스트 커버리지 및 테스트 전략, (6) 문서 품질 및 완전성 Use when this capability is needed.
metadata:
  author: icartsh
---

# Code Reviewer

보안 이슈, 품질 지표, 성능 문제 및 모범 사례 준수 여부를 체계적으로 분석하는 포괄적인 자동화 코드 리뷰 SKILL입니다.

## Purpose

이 SKILL은 자동화된 분석 도구와 전문가 가이드를 결합하여 보안, 품질, 성능 및 유지보수성 측면에서 이슈를 식별하는 구조화된 코드 리뷰 워크플로우를 제공합니다.

## When to Use This Skill

이 SKILL은 다음과 같은 경우에 사용하세요:
- Pull request 또는 코드 제출을 리뷰할 때
- 기존 코드베이스에 대한 보안 감사를 수행할 때
- 배포 전 코드 품질을 평가할 때
- 기술 부채 및 리팩토링 기회를 식별할 때
- 팀을 위한 코드 리뷰 표준을 수립할 때
- 코드 리뷰에서 무엇을 살펴봐야 하는지 학습할 때

## Core Review Workflow

### Phase 1: Initial Analysis

#### 1.1 Context 이해하기
- PR 설명 또는 변경 요약을 읽습니다.
- 변경 유형(기능, 버그 수정, 리팩토링, 보안 패치)을 식별합니다.
- 범위와 영향을 받는 컴포넌트를 결정합니다.
- 관련된 이슈나 티켓이 있는지 확인합니다.

#### 1.2 코드 개요 파악
- 파일 변경 사항과 추가/삭제된 내용을 검토합니다.
- 변경된 모듈과 그 관계를 식별합니다.
- 예상치 못한 변경이나 범위 확장(Scope creep)을 찾습니다.
- Breaking change가 있는지 확인합니다.

### Phase 2: Security Review

#### 2.1 일반적인 취약점 패턴

다음과 같은 중요한 보안 이슈를 확인합니다:

**Input Validation**
- 검증되지 않은 사용자 입력이 민감한 작업에 도달하는지 확인
- SQL injection 취약점
- Command injection 가능성
- Path traversal 공격
- XML/XXE injection 포인트

**Authentication & Authorization**
- 인증 체크 누락
- Broken access control
- 안전하지 않은 비밀번호 저장
- 취약한 세션 관리
- CSRF 보호 누락

**Data Exposure**
- 하드코딩된 자격 증명(Credential) 또는 API key
- 로그 내 민감한 데이터 포함 여부
- 부적절한 암호화
- 에러 메시지를 통한 정보 노출
- 노출된 설정 파일

**Code Injection**
- 안전하지 않은 Deserialization
- Template injection
- 사용자 입력에 의한 코드 실행(Code evaluation)
- 안전하지 않은 Reflection 사용

#### 2.2 자동화 보안 스캔

보안 분석 도구를 사용합니다:

**Python:**
```bash
# 보안 이슈를 위해 bandit 실행
python scripts/review_helper.py --security-scan path/to/code

# 알려진 취약점에 대해 종속성 체크
safety check
pip-audit
```

**JavaScript/Node.js:**
```bash
# 취약점 체크
npm audit
yarn audit

# ESLint 보안 플러그인 사용
eslint --plugin security path/to/code
```

**Go:**
```bash
# 보안 스캐닝
gosec ./...
```

상세한 취약점 패턴은 `references/security_patterns.md`를 참조하세요.

### Phase 3: Code Quality Analysis

#### 3.1 코드 구조

**Modularity & Organization**
- Single Responsibility Principle 준수 여부
- 적절한 Separation of concerns
- 적절한 추상화 수준
- 명확한 모듈 경계
- 논리적인 파일 구성

**Complexity Metrics**
- Cyclomatic complexity (목표: 함수당 < 10)
- 함수 길이 (목표: < 50 라인)
- 클래스 크기 (목표: < 300 라인)
- Nesting depth (목표: < 4 단계)
- 파라미터 개수 (목표: < 5개)

**Code Smells**
- 중복 코드
- 너무 긴 메서드 또는 God class
- Feature envy (메서드가 다른 클래스를 더 많이 사용함)
- Data clumps (반복되는 파라미터 그룹)
- Primitive obsession
- 클래스 간의 부적절한 관계(Inappropriate intimacy)

#### 3.2 명명(Naming) 및 가독성

**Naming Conventions**
- 의도를 드러내는 서술적인 이름
- 일관된 명명 패턴
- 적절한 길이 (너무 짧거나 길지 않게)
- 표준이 아닌 경우 약어 사용 지양
- Boolean 이름은 is/has/should/can으로 시작

**Code Clarity**
- 명확한 Control flow
- 인지 부하 최소화
- Self-documenting code
- 적절한 주석 (What이 아닌 Why에 집중)
- 일관된 포맷팅

#### 3.3 Error Handling

**Robustness**
- 적절한 Exception handling
- 빈 except/catch 블록 지양
- 적절한 에러 메시지
- 리소스 정리 (File handles, connections)
- Graceful degradation

**Edge Cases**
- Null/None 체크
- 빈 컬렉션 처리
- Boundary conditions
- 동시 액세스 이슈
- Race condition 방지

### Phase 4: Performance Review

#### 4.1 일반적인 성능 이슈

**Algorithm Efficiency**
- 더 나은 대안이 있음에도 O(n²) 이상의 알고리즘 사용
- 불필요한 루프 또는 반복
- 비효율적인 데이터 구조 사용
- Memoization/caching 기회 누락

**Resource Management**
- Memory leaks
- 닫히지 않은 File handles 또는 connections
- 과도한 메모리 할당
- Thread/process pool 고갈

**Database Operations**
- N+1 query 문제
- 인덱스 누락
- SELECT * 사용
- 비효율적인 JOIN 작업
- 쿼리 최적화 누락

**Network Calls**
- 동기적 Blocking calls
- Timeout 설정 누락
- 재시도(Retry) 로직 부재
- 과도한 API 호출
- Connection pooling 누락

최적화 전략은 `references/performance_guide.md`를 참조하세요.

### Phase 5: Testing Assessment

#### 5.1 Test Coverage

**Coverage Metrics**
- Line coverage (목표: > 80%)
- Branch coverage (목표: > 75%)
- Function coverage (목표: > 90%)
- Critical path coverage (목표: 100%)

**Test Quality**
- 테스트가 실제로 의미 있는 동작을 검증(Assert)하는지 확인
- 테스트가 독립적이고 격리되어 있는지 확인
- 테스트 이름이 테스트 대상을 명확히 설명하는지 확인
- Mock 및 Stub의 적절한 사용
- 테스트 간 상호 의존성 부재

#### 5.2 Test Completeness

**필수 테스트 유형**
- 비즈니스 로직을 위한 Unit tests
- 컴포넌트 상호작용을 위한 Integration tests
- Edge case 및 boundary 테스트
- 에러 조건 테스트
- 보안 관련 테스트

**누락된 테스트**
- 테스트되지 않은 에러 경로
- 부정적(Negative) 테스트 케이스 누락
- 커버되지 않은 edge condition
- 버그 수정을 위한 Regression tests 부재

### Phase 6: Documentation Review

#### 6.1 코드 문서화

**함수/메서드 문서화**
- 목적 및 동작 설명
- 타입을 포함한 파라미터 설명
- 리턴값 문서화
- Exception 문서화
- 복잡한 API를 위한 사용 예시

**모듈/클래스 문서화**
- 상위 수준의 목적
- 아키텍처 개요
- 설계 결정 사항
- 종속성(Dependencies)
- Public API contracts

#### 6.2 외부 문서화

**README 업데이트**
- 설치 방법
- 설정 변경 사항
- 새로운 기능 문서화
- Breaking change 공지
- Migration 가이드

**API Documentation**
- 엔드포인트 설명
- Request/response 형식
- 인증 요구 사항
- 에러 응답
- Rate limiting

## Review Checklist

포괄적인 리뷰를 위해 이 체크리스트를 사용하세요:

### Security
- [ ] 하드코딩된 자격 증명이나 Secret이 없음
- [ ] 모든 사용자 입력에 대해 Input validation 수행
- [ ] 적절한 Authentication 및 Authorization
- [ ] SQL/Command injection 취약점 없음
- [ ] 안전한 비밀번호 처리
- [ ] 민감한 데이터에 대해 HTTPS/TLS 사용
- [ ] 보안 스캔 도구 실행 완료
- [ ] 종속성 취약점 체크 완료

### Code Quality
- [ ] 함수가 Single Responsibility Principle을 따름
- [ ] Cyclomatic complexity가 10 미만임
- [ ] 코드 중복 없음
- [ ] 일관된 Naming conventions 준수
- [ ] 적절한 Error handling
- [ ] 티켓 번호가 없는 TODO/FIXME 없음
- [ ] 코드가 Self-documenting함

### Performance
- [ ] 명백한 성능 병목 현상이 없음
- [ ] 효율적인 알고리즘 및 데이터 구조 사용
- [ ] 적절한 리소스 정리
- [ ] 데이터베이스 쿼리 최적화 완료
- [ ] N+1 query 문제 없음
- [ ] 적절한 Caching 전략 사용

### Testing
- [ ] 새로운 기능에 대해 테스트 포함됨
- [ ] Edge cases가 커버됨
- [ ] 테스트 커버리지가 표준을 충족함
- [ ] 테스트가 독립적이고 반복 가능함
- [ ] Flaky tests가 도입되지 않음

### Documentation
- [ ] Public API가 문서화됨
- [ ] 복잡한 로직이 설명됨
- [ ] 필요한 경우 README 업데이트됨
- [ ] Breaking changes 문서화됨
- [ ] 필요한 경우 Migration 가이드 제공됨

## Using the Review Helper Script

`scripts/review_helper.py`는 자동화된 분석을 제공합니다:

```bash
# 전체 코드 리뷰 분석
python scripts/review_helper.py --file path/to/file.py --report full

# 보안 중심 스캔
python scripts/review_helper.py --security-scan path/to/directory

# 복잡도 분석
python scripts/review_helper.py --complexity path/to/file.py

# 리뷰 보고서 생성
python scripts/review_helper.py --file path/to/file.py --output report.md
```

## Best Practices

### 리뷰어를 위한 조언 (For Reviewers)

**건설적인 태도 (Be Constructive)**
- 비판이 아닌 개선에 집중하세요.
- 제안 뒤에 숨겨진 "Why"를 설명하세요.
- 대안이나 해결책을 제시하세요.
- 좋은 코드와 패턴은 칭찬하세요.

**철저하지만 효율적인 리뷰 (Be Thorough but Efficient)**
- 반복적인 체크에는 자동화 도구를 사용하세요.
- 사람의 리뷰는 로직과 설계에 집중하세요.
- 스타일에 너무 집착하지 마세요 (Linter 사용).
- 스타일보다 보안과 정확성을 우선시하세요.

**일관성 유지 (Be Consistent)**
- 모든 코드에 동일한 표준을 적용하세요.
- 팀 코딩 표준을 참조하세요.
- 재사용 가능한 리뷰 템플릿을 만드세요.
- 공통된 피드백 패턴을 문서화하세요.

### 코드 작성자를 위한 조언 (For Code Authors)

**리뷰 준비**
- 리뷰를 요청하기 전에 스스로 리뷰(Self-review)하세요.
- Linter와 포맷터를 실행하세요.
- 테스트 슈트를 실행하세요.
- PR 설명에 컨텍스트를 추가하세요.
- 변경 사항을 작고 집중된 단위로 유지하세요.

**피드백 대응**
- 모든 코멘트에 대응하세요.
- 불명확한 경우 질문하세요.
- 피드백을 개인적으로 받아들이지 마세요.
- 완료된 대화는 해결됨(Resolved)으로 표시하세요.

## Common Review Feedback Patterns

### 보안 이슈
```
❌ Security: 하드코딩된 API key 발견
→ 환경 변수 또는 secret management로 이동하세요.
→ 참고: references/security_patterns.md#secrets-management

❌ Security: SQL injection 취약점
→ 문자열 연결 대신 파라미터화된 쿼리를 사용하세요.
→ 예시: cursor.execute("SELECT * FROM users WHERE id = %s", (user_id,))
```

### 품질 이슈
```
❌ Quality: 함수의 복잡도가 너무 높음 (complexity: 15)
→ 더 작고 집중된 기능의 함수들로 나누세요.
→ 목표: < 10 cyclomatic complexity

❌ Quality: 3개 위치에서 중복 코드 발견
→ 공통 로직을 공유 함수로 추출하세요.
→ DRY 원칙 위반
```

### 성능 이슈
```
❌ Performance: N+1 query 문제 탐지됨
→ JOIN 또는 eager loading을 사용하세요.
→ 참고: references/performance_guide.md#database-optimization

❌ Performance: 비효율적인 O(n²) 알고리즘
→ O(1) 조회를 위해 set/hash 사용을 고려하세요.
→ 현재: 중첩 루프, 권장: set intersection
```

## Additional Resources

- **Security Patterns**: `references/security_patterns.md` - 일반적인 취약점 및 해결 방법
- **Performance Guide**: `references/performance_guide.md` - 최적화 전략
- **Review Checklist**: `examples/review_checklist.md` - 포괄적인 리뷰 템플릿
- **Helper Scripts**: `scripts/review_helper.py` - 자동화된 분석 도구

## Language-Specific Considerations

### Python
- Context managers(with 문)의 적절한 사용 확인
- List comprehension이 과도하게 복잡하지 않은지 확인
- Generator 사용 기회 탐색
- Mutable default arguments 확인

### JavaScript/TypeScript
- async/await의 적절한 사용 확인
- Callback hell 확인
- Event listener에서의 메모리 누수 확인
- TypeScript에서의 적절한 Typing 확인

### Java
- 적절한 Exception handling 확인
- 리소스 정리 확인 (try-with-resources)
- Immutability의 적절한 사용 확인
- Thread safety 이슈 확인

### Go
- 적절한 Error handling 확인 (에러 무시 금지)
- Goroutine leak 방지 여부 확인
- Race conditions 확인
- 적절한 Context 사용 확인

## Conclusion

효과적인 코드 리뷰는 자동화된 툴과 사람의 전문성을 결합할 때 이루어집니다. 기계적인 체크(보안, 스타일, 복잡도)에는 자동화 도구를 사용하고, 사람의 리뷰는 로직, 설계 및 유지보수성에 집중하세요. 항상 건설적이고 철저하며 일관성 있는 리뷰를 지향하십시오.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/icartsh) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
