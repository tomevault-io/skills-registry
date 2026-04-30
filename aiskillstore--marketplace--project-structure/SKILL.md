---
name: project-structure
description: Organize project folders following industry best practices. Use when setting up new projects, reorganizing codebases, or when folder structure becomes messy. Covers Next.js, Bulletproof React, and FSD patterns. Use when this capability is needed.
metadata:
  author: aiskillstore
---

# Project Structure

프로젝트 폴더 구조를 업계 표준에 맞게 정리하는 스킬입니다.

## Core Principles

> **"바탕화면에 코드를 두지 않는다"**
> **"분류 기준을 섞지 않는다"**

## Safety Rules

| 명령어 | 상태 | 대안 |
|--------|------|------|
| `rm -rf` | 🔴 **금지** | `_legacy/`로 이동 |
| `rm` | 🔴 **금지** | `_legacy/`로 이동 |
| `mv` to `_legacy/` | ✅ 허용 | 기본 정리 방식 |
| `mkdir` | ✅ 허용 | 새 구조 생성 |

### 정리 방식

```bash
# ❌ NEVER: 삭제
rm -rf old-folder

# ✅ ALWAYS: 레거시 폴더로 이동
mkdir -p _legacy
mv old-folder _legacy/old-folder_$(date +%Y%m%d)
```

---

## Part 1: 개발 루트 디렉토리

### 권장 루트 위치

```bash
~/dev        # 가장 추천
~/code
~/workspace
~/git
```

### 컨텍스트(목적) 중심 구조 (추천)

```
~/dev/
├── work/              # 회사 업무
│   ├── company-a/
│   │   ├── backend-api/
│   │   └── frontend-ui/
│   └── company-b/
├── personal/          # 개인/사이드 프로젝트
│   ├── my-blog/
│   └── todo-app/
├── study/             # 강의/책 실습
│   ├── algorithm-101/
│   └── react-course/
├── open-source/       # Fork/기여 프로젝트
│   └── some-lib/
├── playground/        # 일회성 테스트 (샌드박스)
│   └── test-script.py
└── dotfiles/          # 개인 설정 파일 버전관리
```

### 호스트(Source) 중심 구조 (Go 스타일)

```
~/dev/
├── github.com/
│   ├── my-username/
│   │   └── project-a/
│   └── other-user/
│       └── awesome-lib/
├── gitlab.com/
│   └── company-group/
│       └── company-project/
└── bitbucket.org/
```

---

## Part 2: 프로젝트 내부 구조

### 기본 프로젝트 스캐폴딩

```
project-name/
├── src/              # 실제 소스 코드
├── assets/           # 이미지, 폰트, 정적 파일
├── config/           # 설정 파일
├── docs/             # 문서화 자료
├── scripts/          # 빌드/배포 스크립트
├── tests/            # 테스트 코드
├── dist/             # 빌드 결과물 (Git 제외)
├── _legacy/          # 정리된 레거시 코드
├── .gitignore
├── .env.example      # 환경변수 예시 (.env는 Git 제외)
├── README.md
└── LICENSE
```

---

## Part 3: 프론트엔드 아키텍처 패턴

### Pattern A: Next.js App Router + Colocation

> 라우트(페이지) 기준으로 폴더 생성, 필요한 파일을 같은 폴더에 배치

```
app/
├── (marketing)/           # Route Group (URL에 미반영)
│   ├── page.tsx
│   ├── components/        # 이 라우트 전용 컴포넌트
│   │   └── Hero.tsx
│   └── styles.css
├── dashboard/
│   ├── layout.tsx
│   ├── page.tsx
│   ├── loading.tsx
│   ├── error.tsx
│   └── components/
│       ├── DashboardHeader.tsx
│       └── DashboardStats.tsx
├── api/
│   └── users/
│       └── route.ts
└── globals.css
lib/                       # 공용 유틸리티
components/                # 전역 공용 컴포넌트
```

**적합한 경우**: Next.js 기반 프로젝트

---

### Pattern B: Bulletproof React (Feature-based)

> 기능(Feature) 단위로 묶어서 유지보수 용이한 구조

```
src/
├── app/                   # 앱 초기화 (라우터, 엔트리, 전역 설정)
│   ├── routes/
│   ├── App.tsx
│   └── main.tsx
├── assets/
├── components/            # 완전 공용 UI
│   ├── Button/
│   ├── Modal/
│   └── Form/
├── config/
├── features/              # 🔑 핵심: 기능 단위
│   ├── auth/
│   │   ├── api/
│   │   ├── components/
│   │   ├── hooks/
│   │   ├── types/
│   │   └── index.ts
│   ├── users/
│   │   ├── api/
│   │   ├── components/
│   │   ├── hooks/
│   │   └── index.ts
│   └── dashboard/
├── hooks/                 # 전역 훅
├── lib/                   # 외부 라이브러리 래퍼
├── providers/
├── stores/
├── testing/
├── types/                 # 전역 타입
└── utils/                 # 전역 유틸리티
```

**적합한 경우**: 팀 규모가 크거나 기능이 많은 React 프로젝트

---

### Pattern C: Feature-Sliced Design (FSD)

> 계층(Layer)으로 분류하는 아키텍처 방법론

```
src/
├── app/                   # Layer 1: 앱 초기화
│   ├── providers/
│   ├── styles/
│   └── index.tsx
├── pages/                 # Layer 2: 페이지 (라우트)
│   ├── home/
│   ├── profile/
│   └── settings/
├── widgets/               # Layer 3: 독립적인 UI 블록
│   ├── header/
│   ├── sidebar/
│   └── footer/
├── features/              # Layer 4: 사용자 시나리오
│   ├── auth/
│   ├── comments/
│   └── likes/
├── entities/              # Layer 5: 비즈니스 엔티티
│   ├── user/
│   ├── post/
│   └── comment/
└── shared/                # Layer 6: 공유 리소스
    ├── ui/
    ├── lib/
    ├── api/
    └── config/
```

**적합한 경우**: 규칙을 팀이 같이 지킬 수 있는 중대형 프로젝트

---

## Part 4: 하이브리드 패턴 (Next.js + Feature)

> Next.js App Router를 뼈대로, features 방식을 섞은 실용적 구조

```
app/                       # Next.js App Router
├── (marketing)/
├── dashboard/
└── api/
src/
├── components/            # 전역 공용 컴포넌트
├── features/              # Bulletproof 스타일 기능 단위
│   ├── auth/
│   ├── users/
│   └── analytics/
├── hooks/
├── lib/
├── types/
└── utils/
```

---

## Workflow: 폴더 정리

### 1. 현재 구조 분석

```bash
# 최상위 폴더 확인
ls -la

# 트리 구조 확인 (2단계)
find . -maxdepth 2 -type d | head -30
```

### 2. 레거시 폴더 생성

```bash
mkdir -p _legacy
```

### 3. 정리 대상 이동

```bash
# 날짜 태그 붙여서 이동
mv messy-folder _legacy/messy-folder_$(date +%Y%m%d)
```

### 4. 새 구조 생성

```bash
# Bulletproof 구조 예시
mkdir -p src/{app,assets,components,config,features,hooks,lib,types,utils}
mkdir -p src/features/{auth,users}/{api,components,hooks,types}
```

### 5. 파일 이동

```bash
# 기능별로 파일 이동
mv src/components/LoginForm.tsx src/features/auth/components/
mv src/hooks/useAuth.ts src/features/auth/hooks/
```

---

## Naming Conventions

| 규칙 | 예시 | 설명 |
|------|------|------|
| kebab-case | `my-project` | 폴더명 (공백 금지) |
| PascalCase | `UserProfile.tsx` | React 컴포넌트 |
| camelCase | `useAuth.ts` | 훅, 유틸리티 |
| UPPER_CASE | `API_URL` | 상수 |

## Anti-patterns

```
❌ 언어별 분류
~/dev/python/
~/dev/javascript/
→ React + Django 프로젝트는 어디에?

❌ 바탕화면 사용
~/Desktop/새 폴더/test1/asdf/
→ ~/dev/playground/ 사용

❌ 공백 있는 폴더명
My Project/
→ my-project/

❌ 타입별로만 분류 (규모가 클 때)
src/
├── components/  # 100개 컴포넌트
├── hooks/       # 50개 훅
└── utils/       # 30개 유틸
→ features/ 단위로 그룹화
```

## Quick Setup Scripts

### macOS/Linux: 개발 루트 생성

```bash
mkdir -p ~/dev/{work,personal,study,open-source,playground,dotfiles}
```

### 프로젝트 스캐폴딩

```bash
# 프로젝트 기본 구조
mkdir -p {src,assets,config,docs,scripts,tests,_legacy}
touch README.md .gitignore .env.example
```

### Bulletproof React 구조

```bash
mkdir -p src/{app/routes,assets,components,config,features,hooks,lib,providers,stores,testing,types,utils}
```

---

## References

- [Next.js Project Structure](https://nextjs.org/docs/getting-started/project-structure)
- [Bulletproof React](https://github.com/alan2207/bulletproof-react)
- [Feature-Sliced Design](https://feature-sliced.design)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aiskillstore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
