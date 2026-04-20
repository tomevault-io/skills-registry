---
name: publish
description: This skill should be used when the user asks to "publish a post", "배포", "퍼블리시", or "/publish". Runs the full publishing pipeline - validate, translate, build, LinkedIn post, and deploy guidance. Use when this capability is needed.
metadata:
  author: jeremy-kr
---

# Publishing Pipeline

슬러그를 인자로 받아 전체 퍼블리싱 워크플로우를 실행한다.

## 사용법

```
/publish <slug>
```

## 워크플로우

각 단계는 순차적으로 실행하며, 실패 시 즉시 중단한다.

### 1. 슬러그 검증

- `content/blog/{slug}.mdx` 파일이 존재하는지 확인
- 없으면 에러 메시지와 함께 중단

### 2. 영문 번역 확인

- `content/blog/{slug}.en.mdx` 존재 여부 확인
- 없으면 사용자에게 번역 여부를 질문
  - 번역 원하면: `/translate-post` 스킬 실행 → 번역 완료 후 커밋
  - 번역 건너뛰면: 다음 단계로 진행

### 3. 빌드 검증

- `pnpm build` 실행
- 빌드 실패 시 에러 로그와 함께 중단

### 4. LinkedIn 텍스트 생성

포스트의 frontmatter와 본문을 읽고 LinkedIn용 텍스트를 생성한다:

- 한국어로 작성 (주 독자층)
- 3~4문장으로 포스트 핵심 내용 요약
- 포스트 URL 포함: `https://jeremy.blog/blog/{slug}`
- 포스트 태그에서 해시태그 3~5개 추출 (영문은 그대로, 한글은 `#한글태그` 형식)
- `#개발블로그` 항상 포함
- 이모지 사용 금지
- 총 1300자 이내 (짧을수록 좋음)

### 5. LinkedIn 게시

**환경변수 있는 경우** (`LINKEDIN_ACCESS_TOKEN`, `LINKEDIN_PERSON_URN`):

- 생성한 텍스트를 stdin으로 전달하여 스크립트 실행:
  ```bash
  echo "<텍스트>" | node scripts/linkedin-post.mjs --title "<제목>" --url "https://jeremy.blog/blog/{slug}"
  ```
- 절대 토큰을 로그에 남기지 않는다

**환경변수 없는 경우**:

- 생성한 LinkedIn 텍스트를 화면에 출력
- "아래 텍스트를 LinkedIn에 수동으로 게시하세요" 안내

### 6. 배포 안내

- "git push하면 Vercel이 자동으로 배포합니다" 안내
- 절대 자동으로 git push 하지 않는다
- 현재 커밋되지 않은 변경사항이 있다면 알려준다

## 제약사항

- 절대 자동 push 하지 않음 (안내만)
- LinkedIn 토큰을 로그나 출력에 남기지 않음
- 단계 실패 시 즉시 중단하고 명확한 에러 메시지 출력
- 빌드 검증을 반드시 LinkedIn 게시 전에 수행

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremy-kr) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
