---
name: analyze-nextjs-architecture
description: Next.js 프로젝트의 아키텍처 건전성, 컴포넌트 패턴(서버/클라이언트), 그리고 보안 취약점을 분석합니다. Use when this capability is needed.
metadata:
  author: codefactory-co
---

# Next.js 아키텍처 및 보안 분석 (Analyze Next.js Architecture)

이 스킬은 Next.js 애플리케이션이 **Clean Architecture** 원칙을 잘 따르고 있는지, **Server/Client Component**가 적절히 사용되고 있는지, 그리고 **보안상 위험**한 요소가 없는지 체계적으로 점검하기 위해 사용합니다.

## 사용법

새로운 기능을 구현한 후나 레거시 코드를 리팩토링하기 전에 이 스킬을 실행하여 코드베이스의 건강 상태를 진단하세요. 다음 단계들을 순차적으로 수행하며 발견된 문제점을 리포트합니다.

## 지침

### 1. 렌더링 전략 및 컴포넌트 패턴 분석

가장 먼저 불필요한 클라이언트 컴포넌트 사용을 식별합니다.

1.  **"use client" 검색**: `grep_search`를 사용하여 클라이언트 컴포넌트 사용 현황을 파악합니다.
    ```bash
    grep -r "use client" src/
    ```
2.  **Leaf Node 원칙 검증**:
    *   검색된 파일 중 상위 레이아웃이나 페이지 전체에 `"use client"`가 선언된 경우가 있는지 확인합니다.
    *   `view_file`로 해당 코드를 열어보고, 실제 `useState`, `useEffect`나 이벤트 핸들러가 없는 경우 서버 컴포넌트로 전환을 제안합니다.

### 2. Clean Architecture 의존성 규칙 검증

내부 계층이 외부 계층을 의존하는 위반 사례를 찾습니다.

1.  **Domain 계층 오염 확인**: `src/core/domain` 디렉토리 내의 파일들이 `src/infrastructure`나 `src/app`, `src/components`를 import 하고 있는지 확인합니다.
    ```bash
    grep -r "from '@/infrastructure" src/core/domain
    grep -r "from '@/app" src/core/domain
    grep -r "from '@/components" src/core/domain
    ```
    *   **결과**: 위 명령에서 결과가 나온다면 **심각한 아키텍처 위반**입니다.

2.  **Application 계층 구현체 의존 확인**: `src/core/application` 내에서 구체적인 구현체(Repository Class 등)를 직접 import 하는지 확인합니다. 오직 `interface`만 의존해야 합니다.

### 3. 보안 취약점 감사 (Security Audit)

Next.js 및 웹 애플리케이션의 일반적인 보안 위험을 점검합니다.

1.  **민감 정보 노출 (Sensitive Data Props)**:
    *   클라이언트 컴포넌트로 전달되는 props 중 민감한 이름이 있는지 확인합니다.
    *   패턴: `token`, `password`, `secret`, `key` 등.
    *   *주의*: 서버 컴포넌트에서 클라이언트 컴포넌트로 이 데이터를 넘기는 순간 브라우저 번들에 포함되어 노출됩니다.

2.  **XSS 위험 (dangerouslySetInnerHTML)**:
    *   `dangerouslySetInnerHTML` 사용처를 검색합니다.
    *   사용된 경우, 해당 데이터가 `dompurify` 등으로 살균(sanitize) 처리되었는지 `view_file`로 확인합니다.

3.  **Server Actions 보안**:
    *   `"use server"`가 선언된 파일(`src/app/actions.ts` 등)을 검사합니다.
    *   **입력 검증**: Zod 등을 사용하여 입력값(`FormData` 등)을 검증하고 있는지 확인합니다.
    *   **권한 검사**: 액션 함수 시작 부분에 인증/인가 체크(예: `getUser()`, `assertAuthenticated()`)가 있는지 확인합니다. 이것이 없다면 누구나 API를 호출해 데이터를 조작할 수 있습니다.

### 4. 성능 및 최적화 점검

1.  **무거운 패키지 import**: 클라이언트 컴포넌트에서 거대한 라이브러리(예: 전체 `lodash`, 무거운 차트 라이브러리 등)를 import 하는지 확인합니다. 가능하면 dynamic import를 제안합니다.

## 결과 보고 형식

분석을 마치면 다음과 같은 요약 리포트를 작성하세요:

- **✅ 양호**: 발견된 문제 없음.
- **⚠️ 경고**: 성능 이슈나 경미한 패턴 위반 (예: 불필요한 use client).
- **🚨 위험**: 보안 취약점(XSS, 민감 정보 노출) 또는 심각한 아키텍처 원칙 위반.

각 문제에 대해 수정 제안을 함께 제공하세요.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/codefactory-co) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
