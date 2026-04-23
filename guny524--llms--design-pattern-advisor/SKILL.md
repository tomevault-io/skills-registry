---
name: design-pattern-advisor
description: 코드 작성/수정, 리팩토링, TODO 작성 시 자동 활성화. 복잡도 증가, 조건문 남발, 중복 코드 발견 시 적절한 디자인 패턴 제안. OO 설계 원칙(개방-폐쇄, 단일책임, 의존성역전) 위반 감지하고 Strategy, Observer, Decorator, Factory 등으로 리팩토링 방향 제시. 긴 if-else, switch문, 상속 남용, 새 기능 추가 시 기존 코드 수정 필요 등 code smell 발견 시 사용. Use when this capability is needed.
metadata:
  author: guny524
---

# 디자인 패턴 판단 및 적용 가이드
코드를 작성하거나 리팩토링할 때 적절한 디자인 패턴을 선택하고, OO 설계 원칙에 따라 판단하도록 돕습니다.

## 1. 핵심 OO 설계 원칙

### 1-1. 캡슐화 (Encapsulation)
"Identify the aspects that vary and separate them from what stays the same."
- 위반 징후: 하나의 요구사항 변경이 여러 클래스 수정을 유발

### 1-2. 인터페이스 프로그래밍
"Program to an interface, not an implementation."
- 위반 징후: `new ConcreteClass()` 직접 호출이 많음

### 1-3. 구성 우선 (Composition over Inheritance)
"Favor composition over inheritance."
- 위반 징후: 깊은 상속 계층, 여러 클래스에 같은 코드 복사

### 1-4. 느슨한 결합 (Loose Coupling)
"Strive for loosely coupled designs between objects that interact."
- 위반 징후: 한 클래스 변경이 연쇄적으로 다른 클래스 수정 요구

### 1-5. 개방-폐쇄 원칙 (Open-Closed Principle)
"Classes should be open for extension, but closed for modification."
- 위반 징후: 새 기능 추가 시마다 기존 클래스 수정 필요

### 1-6. 의존성 역전 원칙 (Dependency Inversion)
"Depend upon abstractions. Do not depend upon concrete classes."
- 위반 징후: 구체 클래스에 직접 의존하는 코드가 많음

### 1-7. 단일 책임 원칙 (Single Responsibility)
"A class should have only one reason to change."
- 위반 징후: 클래스가 여러 이유로 변경될 수 있음

### 1-8. 최소 지식 원칙 (Principle of Least Knowledge)
"Talk only to your immediate friends."
- 위반 징후: 메서드 체이닝이 많음 (`a.getB().getC().doSomething()`)

## 2. 패턴 선택 Decision Tree
```
1. 알고리즘/동작을 런타임에 선택/변경해야 하는가?
   ├─ YES → Strategy Pattern 검토
   └─ NO ↓

2. 한 객체의 상태 변화를 여러 객체에 알려야 하는가?
   ├─ YES → Observer Pattern 검토
   └─ NO ↓

3. 기존 객체에 동적으로 기능을 추가해야 하는가?
   ├─ YES → Decorator Pattern 검토
   └─ NO ↓

4. 객체 생성 과정을 캡슐화해야 하는가?
   ├─ YES → Factory Pattern 검토
   └─ NO ↓

5. 단 하나의 인스턴스만 필요한가?
   ├─ YES → Singleton Pattern 검토
   └─ NO ↓

6. 요청을 객체로 캡슐화해야 하는가?
   ├─ YES → Command Pattern 검토
   └─ NO ↓

7. 호환되지 않는 인터페이스를 변환해야 하는가?
   ├─ YES → Adapter Pattern 검토
   └─ NO ↓

8. 복잡한 시스템을 단순한 인터페이스로 제공해야 하는가?
   ├─ YES → Facade Pattern 검토
   └─ NO ↓

9. 알고리즘의 뼈대는 유지하고 세부 단계만 변경하는가?
   ├─ YES → Template Method Pattern 검토
   └─ NO ↓

10. 객체의 상태에 따라 동작이 달라지는가?
    ├─ YES → State Pattern 검토
    └─ NO ↓

11. 객체에 대한 접근을 제어해야 하는가?
    └─ YES → Proxy Pattern 검토
```

## 3. 패턴 레퍼런스
패턴 정의, 구조 다이어그램, Before/After 예시는 [REFERENCE.md](~/llms/skills/design-pattern-advisor/REFERENCE.md) 참조. Decision Tree로 후보를 좁힌 뒤 해당 패턴 섹션만 읽으면 컨텍스트를 최소로 유지할 수 있습니다.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/guny524) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
