---
name: frontend
description: Frontend development best practices and tools. Routes to the appropriate sub-skill. Trigger on "React", "Next.js", "프론트엔드", "컴포넌트", "deploy", "배포", "UI 디자인", "UI 리뷰", "성능 최적화", "React Native", "모바일 앱", "풀스택", "FSD", "Feature-Sliced Design", "Playwright", "E2E 테스트", "랜딩페이지", "웹 디자인", "인터페이스 디자인", "frontend design". Sub-skills — react-best-practices (React/Next.js performance optimization), composition-patterns (React composition and component architecture), react-native-skills (React Native/Expo mobile best practices), frontend-design (distinctive, production-grade frontend interface design with bold aesthetics), vercel-deploy-claimable (Vercel deployment), nextjs-fullstack (Next.js 15 fullstack with FSD architecture, ShadCN, Jotai, Playwright, "풀스택 개발", "FSD 구조", "E2E 테스트"). Use when this capability is needed.
metadata:
  author: seungwonme
---

# Frontend: Development Skills Router

Route to the correct sub-skill based on the user's need.

## Sub-skill Selection

| Sub-skill                    | When                                                        | Read                                    |
| ---------------------------- | ----------------------------------------------------------- | --------------------------------------- |
| **nextjs-fullstack**         | Next.js 15 fullstack, FSD architecture, ShadCN, Playwright  | `nextjs-fullstack/SKILL.md`             |
| **react-best-practices**     | React/Next.js performance optimization, data fetching, SSR  | `react-best-practices/SKILL.md`         |
| **composition-patterns**     | Component architecture, compound components, prop refactor  | `composition-patterns/SKILL.md`         |
| **react-native-skills**      | React Native/Expo mobile app development and optimization   | `react-native-skills/SKILL.md`          |
| **frontend-design**          | Distinctive UI design, creative aesthetics, visual identity  | `frontend-design/SKILL.md`             |
| **vercel-deploy-claimable**  | Deploy app to Vercel, get preview URL                       | `vercel-deploy-claimable/SKILL.md`      |

## Decision Logic

1. **Next.js 풀스택 프로젝트 셋업·구조·컨벤션** → `nextjs-fullstack` (FSD, ShadCN, Jotai, Playwright, Toss 원칙)
2. **React/Next.js 코드 작성·리뷰·최적화** → `react-best-practices`
3. **컴포넌트 설계·리팩토링·아키텍처** → `composition-patterns`
4. **React Native/Expo 모바일 개발** → `react-native-skills`
5. **독창적 UI 디자인·미학적 인터페이스·시각 아이덴티티** → `frontend-design`
6. **Vercel 배포** → `vercel-deploy-claimable`

If unclear which sub-skill fits, use AskUserQuestion:

```
questions:
  - question: "어떤 프론트엔드 작업이 필요한가요?"
    header: "유형"
    options:
      - label: "Next.js 풀스택 개발"
        description: "FSD 아키텍처, 프로젝트 구조, 컨벤션, E2E 테스트, 디자인 규칙"
      - label: "React/Next.js 최적화"
        description: "성능 최적화, 데이터 페칭, 번들 사이즈, SSR/CSR 패턴"
      - label: "컴포넌트 아키텍처"
        description: "컴포넌트 구조 설계, compound components, prop 리팩토링"
      - label: "UI 디자인 / React Native / 배포"
        description: "독창적 인터페이스 디자인, 모바일 앱, Vercel 배포"
    multiSelect: false
```

After selection, read the corresponding sub-skill's SKILL.md and follow its protocol.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/seungwonme) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
