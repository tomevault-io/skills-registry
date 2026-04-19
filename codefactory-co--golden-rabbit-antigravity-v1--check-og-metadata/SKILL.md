---
name: check-og-metadata
description: Next.js 프로젝트의 Open Graph (OG) 메타태그 및 SEO 메타데이터 적용 상태를 분석하고 독립적 구현이 필요한 페이지를 추천합니다. Use when this capability is needed.
metadata:
  author: codefactory-co
---

# Open Graph (OG) 및 SEO 메타데이터 분석

이 스킬은 Next.js 애플리케이션이 소셜 미디어 공유 및 SEO를 위한 메타데이터를 적절히 구현했는지 점검합니다. 단순한 존재 여부 확인을 넘어, **공유 가치가 높은 페이지**를 식별하고 구체적인 메타데이터 전략을 제안하는 것이 목표입니다.

## 사용법

프로젝트의 메타데이터 상태를 진단하고 싶을 때 실행하세요.

## 지침

### 1. 전역 메타데이터 (Global Metadata) 점검

`src/app/layout.tsx` (Root Layout) 파일을 검사하여 기본 설정이 되어 있는지 확인합니다.

1.  **필수 항목 확인**:
    *   `metadataBase`: 절대 경로 URL 생성을 위해 필수입니다 (Warning 방지).
    *   `title.template`: 하위 페이지에서 타이틀을 쉽게 조합하기 위해 설정되어 있는지 확인합니다.
    *   `openGraph`: 기본 `title`, `description`, `images`, `type`이 설정되어 있는지 확인합니다.

    ```bash
    grep -E "metadataBase|openGraph|twitter" src/app/layout.tsx
    ```

### 2. 동적 페이지 (Dynamic Routes) 메타데이터 점검

`[id]` 또는 `[slug]`와 같은 동적 라우트 페이지를 찾고, `generateMetadata` 함수가 구현되어 있는지 확인합니다.

1.  **동적 라우트 식별**:
    ```bash
    find src/app -name "page.tsx" | grep "\["
    ```
2.  **generateMetadata 구현 확인**:
    *   식별된 파일에 `generateMetadata`가 export 되어 있는지 확인합니다 (`grep "export async function generateMetadata"`).
    *   `view_file`로 코드를 열어, DB나 API에서 데이터를 가져와 `title`과 `openGraph`를 동적으로 생성하는지 확인합니다.

### 3. 고유 메타데이터 추천 (High-Value Pages Recommendation)

독립적인 메타데이터가 구현되어야 할 중요한 페이지를 식별하고 추천합니다.

1.  **공유 가치 식별**:
    *   다음과 같은 페이지는 반드시 독립된 OG 태그가 필요합니다:
        *   **상세 페이지**: 상품 상세, 글 상세, 강의 상세 등 (`/products/[id]`, `/notes/[id]`) -> 해당 콘텐츠의 제목과 요약 필요.
        *   **마케팅 페이지**: 랜딩, 가격 정책, 이벤트 페이지 -> 클릭을 유도하는 매력적인 카피 필요.
        *   **진입 페이지**: 로그인, 회원가입 -> 서비스의 신뢰도를 주는 설명 필요.

2.  **구현 추천 리포트 작성**:
    *   `generateMetadata`가 누락된 동적 페이지나, `metadata` 객체가 없는 주요 정적 페이지를 나열합니다.
    *   각 페이지에 적합한 `title`, `description`, `openGraph` 예시 코드를 제안합니다.

## 결과 보고 형식

분석 후 다음 리포트를 작성하세요:

- **✅ 전역 설정**: `metadataBase` 등 필수 값 설정 여부.
- **🔍 분석 결과**:
    - **동적 라우트**: `generateMetadata` 구현 현황.
    - **주요 페이지**: 개별 메타데이터 적용 현황.
- **💡 추천 사항**:
    - 독립적 메타데이터가 필요한 페이지 목록.
    - 각 페이지별 추천 메타데이터 코드 (예: `generateMetadata` 구현 예시).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/codefactory-co) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
