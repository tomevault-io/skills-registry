---
name: obsidian-add-content
description: Obsidian vault에 새로운 Frontend 지식 또는 Team Sparta 프로젝트를 템플릿 기반으로 추가합니다. 폴더 존재 여부를 확인하고 필요시 생성합니다. Use when this capability is needed.
metadata:
  author: jun9898
---

# Obsidian Add Content Skill

이 Skill은 Obsidian vault에 새로운 콘텐츠를 템플릿 기반으로 추가합니다.

## Vault 경로
`/Users/teamsparta/Library/Mobile Documents/iCloud~md~obsidian/Documents/for-dev`

## 템플릿 경로
- **Information 템플릿**: `/Users/teamsparta/Library/Mobile Documents/iCloud~md~obsidian/Documents/for-dev/template/Information.md`
- **Repository 템플릿**: `/Users/teamsparta/Library/Mobile Documents/iCloud~md~obsidian/Documents/for-dev/template/Repository.md`
- **Code 템플릿**: `/Users/teamsparta/Library/Mobile Documents/iCloud~md~obsidian/Documents/for-dev/template/Code.md`

---

## 사용 시나리오

### 1. Frontend 라이브러리/기능 추가

**사용자 입력 예시**:
- "React의 useEffect 추가해줘"
- "Next의 getServerSideProps 추가"
- "Vanilla의 Event Delegation 추가"

**동작**:
1. 입력에서 라이브러리명(React/Next/Vanilla 등)과 기능명 추출
2. `Frontend/{라이브러리}/` 폴더 확인
   - 없으면: 폴더 생성 + `{라이브러리}.md` 폴더 노트 생성 (Information 템플릿)
   - 있으면: 기존 폴더 사용
3. `Frontend/{라이브러리}/{기능명}.md` 생성 (Information 템플릿)
4. Frontmatter 자동 설정:
   - `created-at`: 오늘 날짜 (YYYY-MM-DD)
   - `tags`: 자동 추천 (아래 로직 참고)
   - `color`: 빈 문자열
5. 제목과 기본 섹션 구조 생성

### 2. Team Sparta 프로젝트 추가

**사용자 입력 예시**:
- "모두AI 프로젝트 추가해줘"
- "아카데미아 repo 추가"

**동작**:
1. 입력에서 프로젝트명 추출
2. `Team Sparta/repo/{프로젝트명}/` 폴더 생성
3. `Team Sparta/repo/{프로젝트명}/{프로젝트명}.md` 생성 (Repository 템플릿)
4. Frontmatter 자동 설정:
   - `created-at`: 오늘 날짜
   - `tags`: `[team-sparta, project]`
   - `color`: 빈 문자열
   - `tech-stack`: 빈 배열 (사용자가 채움)
   - `project-type`: 빈 문자열 (사용자가 채움)
   - `description`: 빈 문자열 (사용자가 채움)
5. 제목과 기본 섹션 구조 생성

### 3. Repo 코드 파일 추가

**사용자 입력 예시**:
- "dev_proxy의 config 추가"
- "모두AI의 utils 추가"
- "/Users/teamsparta/repo/modoo-ai-frontend/src/features/product-tour 의 스켈레톤 만들어줘"

**동작**:

#### A. 단일 파일 추가 (간단한 입력)
1. 프로젝트 폴더 확인 (없으면 생성)
2. `{프로젝트}/{파일명}.md` 생성 (Code 템플릿)
   - frontmatter:
     - `created-at`: 현재 날짜
     - `tags`: `[team-sparta, code]`
     - `color`: 빈 문자열
     - `file-path`: 빈 문자열 (사용자가 채움)
     - `language`: 입력에서 추론 (config.js → javascript, utils.py → python)
   - 제목: 파일명
   - "## 코드" 섹션: 해당 언어의 빈 코드 블록 생성
3. 사용자가 "## 코드" 섹션에 실제 코드 작성
4. **코드 분석이나 설명 작성은 하지 않음** - 이는 `obsidian-refine-content` Skill의 역할

#### B. 디렉토리 구조 기반 스켈레톤 생성 (경로 입력)
사용자가 실제 파일 시스템 경로를 입력한 경우:

**예시**: `/Users/teamsparta/repo/modoo-ai-frontend/src/features/product-tour`

**동작**:
1. **경로 파싱**:
   - `/Users/teamsparta/repo/` 이후의 경로 추출
   - 프로젝트명 식별: `modoo-ai-frontend`
   - 하위 경로 추출: `src/features/product-tour`

2. **프로젝트 폴더 생성**:
   - `Team Sparta/repo/{프로젝트명}/` 폴더 생성
   - `{프로젝트명}.md` 프로젝트 문서 생성 (Repository 템플릿)

3. **실제 디렉토리 구조 스캔**:
   - 입력된 경로의 모든 소스 파일 탐색 (`.ts`, `.tsx`, `.js`, `.jsx`, `.py` 등)
   - 각 파일의 상대 경로 파악

4. **Obsidian 디렉토리 구조 재현**:
   - 원본 프로젝트의 폴더 구조를 Obsidian vault에 **완전히 동일하게** 재현
   - 예시:
     ```
     원본: /repo/modoo-ai-frontend/src/features/product-tour/contexts/context.ts
     생성: Team Sparta/repo/modoo-ai-frontend/src/features/product-tour/contexts/context.md

     원본: /repo/modoo-ai-frontend/src/features/product-tour/components/button.tsx
     생성: Team Sparta/repo/modoo-ai-frontend/src/features/product-tour/components/button.md
     ```
   - **중요**: 전체 경로를 그대로 유지! `src/features/product-tour/` 같은 중간 경로도 모두 포함
   - 실제 레포와 동일한 문서 구조를 만드는 것이 목표

5. **각 파일에 대해 Code 템플릿 기반 문서 생성**:
   - frontmatter:
     - `created-at`: 현재 날짜
     - `tags`: `[team-sparta, code]`
     - `file-path`: 실제 파일의 절대 경로
     - `language`: 파일 확장자에서 추론 (`.tsx` → `tsx`, `.ts` → `typescript`)
   - 제목: 파일명 (확장자 제외)
   - "## 코드" 섹션: **실제 파일의 코드를 읽어서 삽입** (Read tool 사용)

6. **폴더별 그룹화**:
   - 동일한 폴더의 파일들은 같은 Obsidian 하위 폴더에 생성
   - 예: `contexts/`, `components/`, `hooks/`, `helpers/` 등

**생성 예시**:
```
Team Sparta/repo/modoo-ai-frontend/
├── modoo-ai-frontend.md (프로젝트 문서)
└── src/
    └── features/
        └── product-tour/
            ├── contexts/
            │   └── product-tour-context.md
            ├── constants/
            │   ├── product-tour-status.md
            │   └── product-tour-config.md
            ├── components/
            │   ├── product-tour.md
            │   └── tooltip/
            │       └── product-tour-tooltip.md
            ├── hooks/
            │   └── use-product-tour-handlers.md
            └── helpers/
                └── product-tour-cookie-helpers.md
```

**코드 파일 예시**:
```markdown
# product-tour-context

## 개요


## 코드

\`\`\`typescript
// 실제 파일에서 읽어온 코드가 여기에 들어갑니다
import { createContext } from 'react';
...
\`\`\`

## 주요 함수/클래스


## 사용 방법

```

---

## 태그 자동 추천 로직

### Frontend 파일의 태그 추천

**기본 태그** (항상 포함):
- `front-end`
- 라이브러리명을 소문자로 (예: `react`, `next`, `vanilla-js`)

**추가 태그** (키워드 기반):
- `use`로 시작하는 이름 (useEffect, useState 등) → `hooks` 추가
- `routing`, `router`, `route` 포함 → `routing` 추가
- `form`, `input`, `validation` 포함 → `forms` 추가
- `context`, `zustand`, `redux`, `state` 포함 → `state-management` 추가
- `component` 포함 → `components` 추가
- `seo`, `meta`, `head` 포함 → `seo` 추가

**기존 파일 참고**:
- 같은 폴더의 기존 .md 파일들을 검색
- 공통 태그가 있으면 참고하여 추가

**예시**:
- `useEffect.md` → `tags: [front-end, react, hooks]`
- `Hash Routing.md` → `tags: [front-end, vanilla-js, routing]`
- `Controlled Form.md` → `tags: [front-end, react, forms]`
- `Context Api.md` → `tags: [front-end, react, state-management]`

### Repository 파일의 태그 추천

**기본 태그**:
- `team-sparta`
- `project`

---

## 출력 형식

성공 시:
```
✅ 새 파일 생성 완료!

📁 경로: Frontend/React/useEffect.md
🏷️  태그: front-end, react, hooks

이제 파일을 열어서 내용을 작성하세요.
작성 후 `obsidian-refine-content` Skill로 정리/보완할 수 있습니다.
```

폴더도 생성한 경우:
```
✅ 새 라이브러리 폴더 및 파일 생성 완료!

📁 폴더: Frontend/Next/
📄 폴더 노트: Frontend/Next/Next.md
📄 기능 파일: Frontend/Next/getServerSideProps.md
🏷️  태그: front-end, next

이제 파일들을 열어서 내용을 작성하세요.
```

단일 코드 파일 추가 시:
```
✅ 코드 문서 생성 완료!

📄 파일: Team Sparta/repo/dev_proxy/config.md
🏷️  태그: team-sparta, code
💻 언어: javascript

이제 config.md를 열어서 "## 코드" 섹션에 코드를 작성하세요.
작성 후 `obsidian-refine-content` Skill로 코드 분석 및 설명이 자동 생성됩니다.
```

디렉토리 구조 기반 스켈레톤 생성 시:
```
✅ product-tour 스켈레톤 생성 완료!

📁 프로젝트 경로: Team Sparta/repo/modoo-ai-frontend/

생성된 파일 목록 (총 14개)

1. 프로젝트 문서
   - modoo-ai-frontend.md

2. src/features/product-tour/contexts/ (1개)
   - product-tour-context.md

3. src/features/product-tour/constants/ (2개)
   - product-tour-status.md
   - product-tour-config.md

4. src/features/product-tour/components/ (5개)
   - product-tour.md
   - tooltip/product-tour-tooltip.md
   ...

🏷️  태그: team-sparta, code
💻 전체 디렉토리 경로가 원본과 동일하게 유지됨

각 파일의 "## 코드" 섹션에 실제 코드를 작성한 후,
`obsidian-refine-content` Skill로 자동 분석 및 설명을 생성하세요.
```

---

## 예외 처리

1. **파일이 이미 존재하는 경우**:
   - 메시지: "⚠️ 파일이 이미 존재합니다: {경로}. 덮어쓰시겠습니까?"
   - 사용자 확인 후 진행

2. **라이브러리명/프로젝트명을 파싱할 수 없는 경우**:
   - 사용자에게 명확한 입력 요청
   - 예: "라이브러리명과 기능명을 명확히 입력해주세요. 예: 'React의 useEffect 추가'"

3. **업무일지 폴더에 추가하려는 경우**:
   - 메시지: "❌ 업무일지 폴더는 자동 생성 대상이 아닙니다. 수동으로 작성해주세요."

---

## 중요 지시사항

1. **날짜 형식**: `created-at`은 항상 `YYYY-MM-DD` 형식 (예: 2025-12-18)
2. **태그 형식**: 배열 형식, kebab-case 사용 (예: `front-end`, `state-management`)
3. **파일명**: 사용자 입력 그대로 사용, 공백 포함 가능 (예: `Context Api.md`, `Hash Routing.md`)
4. **폴더 노트**: 라이브러리 폴더 생성 시 폴더 노트도 함께 생성
5. **템플릿 정확히 따르기**: Information, Repository, Code 템플릿의 구조를 정확히 따라야 함
6. **Code 템플릿 사용 시**:
   - .md 파일만 생성 (실제 코드 파일은 만들지 않음)
   - "## 코드" 섹션에 빈 코드 블록 생성
   - 언어는 사용자 입력에서 추론 (config.js → javascript)
   - 코드 분석/설명은 작성하지 않음 (Skill 2의 역할)

---

## 작업 순서

### A. 단일 파일 추가
1. 사용자 입력 파싱
2. 대상 경로 결정
3. 폴더 존재 확인 → 필요시 생성
4. 템플릿 파일 읽기
5. 태그 자동 추천
6. 새 파일 생성 (템플릿 + 메타데이터)
7. 성공 메시지 출력

### B. 디렉토리 스켈레톤 생성 (경로 입력 시)
1. 입력된 실제 파일 시스템 경로 파싱
2. 프로젝트명 추출 (`/Users/teamsparta/repo/` 이후 첫 번째 폴더)
3. 프로젝트 폴더 및 문서 생성 (`Team Sparta/repo/{프로젝트명}/`)
4. 입력된 경로의 모든 소스 파일 스캔 (`.ts`, `.tsx`, `.js`, `.jsx` 등)
5. **각 파일의 전체 상대 경로 추출** (예: `src/features/product-tour/contexts/context.ts`)
6. **Obsidian vault에 전체 경로를 동일하게 재현**:
   - `Team Sparta/repo/{프로젝트명}/src/features/product-tour/contexts/context.md`
   - 모든 중간 폴더 생성 필요
7. 각 파일에 대해 Code 템플릿 기반 문서 생성:
   - `file-path`에 원본 파일의 절대 경로 기록
   - `language`는 확장자에서 자동 추론
   - **Read tool로 실제 파일 내용 읽기**
   - **읽은 코드를 "## 코드" 섹션에 삽입**
8. 성공 메시지 출력 (생성된 파일 개수 및 경로 구조 표시)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jun9898) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
