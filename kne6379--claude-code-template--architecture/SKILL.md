---
name: architecture-guide
description: 코드 작성 시 프로젝트 아키텍처 가이드를 자동으로 적용합니다. Use when this capability is needed.
metadata:
  author: kne6379
---

# 프로젝트 아키텍처 가이드

이 가이드는 프로젝트의 코드 품질과 일관성을 유지하기 위한 범용 원칙입니다.

## 코드 구조 원칙

### 1. 단일 책임 원칙 (SRP)
- 파일/모듈당 하나의 주요 책임만 가집니다
- 함수는 하나의 작업만 수행합니다
- 클래스는 하나의 변경 이유만 가집니다

### 2. 계층 분리
```
[Presentation Layer] → [Business Logic] → [Data Access] → [External Services]
```
- 각 계층은 자신보다 하위 계층만 의존합니다
- 순환 의존성은 금지됩니다

### 3. 의존성 주입
- 하드코딩된 의존성 대신 주입을 사용합니다
- 이를 통해 테스트 용이성을 확보합니다

## 네이밍 컨벤션

### 일반 규칙
- 변수/함수: `camelCase`를 사용합니다
- 클래스/타입: `PascalCase`를 사용합니다
- 상수: `SCREAMING_SNAKE_CASE`를 사용합니다
- 파일명: 언어/프레임워크 컨벤션을 준수합니다

### 의미 있는 이름
- 함수명은 동사로 시작합니다 (예: `getUserById`, `calculateTotal`)
- 변수명은 명사를 사용합니다 (예: `userList`, `totalAmount`)
- Boolean은 is/has/can 접두사를 붙입니다 (예: `isActive`, `hasPermission`)

## 에러 처리

### 원칙
- 에러는 적절한 계층에서 처리합니다
- 복구 가능한 에러와 치명적 에러를 구분합니다
- 의미 있는 에러 메시지를 제공합니다

### 패턴
- 비즈니스 로직에서 예외를 정의합니다
- 프레젠테이션 계층에서 사용자 친화적 메시지로 변환합니다
- 로깅은 적절한 레벨로 수행합니다

## 코드 품질

### DRY (Don't Repeat Yourself)
- 중복 코드는 함수/모듈로 추출합니다
- 단, 과도한 추상화는 지양합니다

### KISS (Keep It Simple, Stupid)
- 불필요한 복잡성을 제거합니다
- 명확하고 읽기 쉬운 코드를 우선합니다

### YAGNI (You Aren't Gonna Need It)
- 현재 필요한 기능만 구현합니다
- 미래의 가상 요구사항은 배제합니다

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kne6379) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
