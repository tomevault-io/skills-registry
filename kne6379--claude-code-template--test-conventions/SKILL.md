---
name: test-conventions
description: 테스트 작성 시 컨벤션을 자동으로 적용합니다. Use when this capability is needed.
metadata:
  author: kne6379
---

# 테스트 컨벤션 가이드

이 가이드는 언어와 프레임워크에 관계없이 적용되는 테스트 작성 원칙입니다.

## 테스트 구조

### AAA 패턴 (Arrange-Act-Assert)

모든 테스트는 세 단계로 구성합니다:

```
1. Arrange (준비)
   - 테스트에 필요한 데이터/객체를 생성합니다
   - Mock/Stub을 설정합니다

2. Act (실행)
   - 테스트 대상 함수/메서드를 호출합니다

3. Assert (검증)
   - 기대 결과와 실제 결과를 비교합니다
```

### 테스트 그룹화

```
[테스트 대상]
  └── [기능/메서드]
        ├── 정상 케이스
        ├── 경계값 케이스
        └── 에러 케이스
```

## 필수 테스트 케이스

### 1. 정상 케이스 (Happy Path)
- 기본 동작을 확인합니다
- 다양한 유효 입력값을 테스트합니다

### 2. 경계값 테스트 (Edge Cases)
- 빈 값(null, undefined, 빈 문자열, 빈 배열)을 테스트합니다
- 최소/최대값을 테스트합니다
- 경계 조건(off-by-one)을 확인합니다

### 3. 에러 케이스 (Error Cases)
- 잘못된 입력 타입을 테스트합니다
- 범위 초과 값을 테스트합니다
- 예외 상황 처리를 검증합니다

## 네이밍 규칙

### 테스트 이름 패턴

```
should [예상 동작] when [조건]
```

예시:
- "should return empty list when no items exist"
- "should throw error when input is null"
- "should calculate total correctly when discount applied"

### 피해야 할 이름
- "test1", "works", "should work" 등 의미 없는 이름은 사용하지 않습니다
- 구현 세부사항에 의존하는 이름은 사용하지 않습니다

## Mock 사용 원칙

### Mock 대상
- 외부 API 호출을 Mock합니다
- 데이터베이스 연결을 Mock합니다
- 파일 시스템 접근을 Mock합니다
- 시간 관련 함수를 Mock합니다

### Mock 지양
- 내부 구현 세부사항은 Mock하지 않습니다
- 같은 모듈 내 함수는 Mock하지 않습니다
- 테스트 대상 자체는 Mock하지 않습니다

## 테스트 품질

### 독립성
- 각 테스트는 다른 테스트에 의존하지 않아야 합니다
- 테스트 순서에 상관없이 동일한 결과를 보장합니다
- 테스트 간 공유 상태는 금지합니다

### 결정성
- 동일 입력에 대해 항상 동일한 결과를 반환합니다
- 랜덤 값 사용 시 시드를 고정합니다
- 시간 의존적 테스트는 Mock을 사용합니다

### 가독성
- 테스트 코드도 깨끗하게 유지합니다
- 매직 넘버 대신 명명된 상수를 사용합니다
- 복잡한 설정은 헬퍼 함수로 추출합니다

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kne6379) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
