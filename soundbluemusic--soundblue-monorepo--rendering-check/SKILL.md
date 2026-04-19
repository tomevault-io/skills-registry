---
name: rendering-check
description: SEO 호환성 검증 - 브라우저 API 사용 및 이중 구현 확인, SPA 감지 시 경고 Use when this capability is needed.
metadata:
  author: soundbluemusic
---

# /rendering-check 스킬

프로젝트의 SEO 호환 렌더링(SSG/SSR)을 검증하고, **SPA 모드 발견 시 경고**합니다.

## 사용법

```
/rendering-check [경로 또는 패키지명]
```

## 예시

```
/rendering-check                          # 전체 프로젝트 검사
/rendering-check packages/core/           # core 레이어만 검사
/rendering-check packages/platform/storage # 특정 패키지 검사
```

## 검사 항목

### 1. core/ 레이어 (브라우저 API 금지)

```
❌ window, document, navigator, localStorage, sessionStorage
❌ Web Audio API, IndexedDB, fetch (브라우저 전용)
✅ 순수 TypeScript 로직만 허용
```

### 2. platform/ 레이어 (이중 구현 필수)

```
✅ *.browser.ts + *.noop.ts 쌍 존재 확인
✅ package.json exports 설정 확인
   - "browser": "./src/index.browser.ts"
   - "default": "./src/index.noop.ts"
```

### 3. apps/ 레이어 (SEO 호환 렌더링 확인)

```
✅ SSR 모드: TanStack Start + Cloudflare Workers (기본값)
❌ SPA 모드: SSR 비활성화 (금지)
```

## SPA 감지 조건

다음 조건이 해당되면 SPA로 판정 (SEO 위험):

1. TanStack Start SSR이 비활성화됨
2. 클라이언트 사이드 렌더링만 사용

## 실행 규칙

1. Task tool 호출
2. `subagent_type: "Explore"` 지정
3. 검사 대상 파일들을 탐색
4. 위반 사항 목록 반환

## 결과 형식

```markdown
## SEO 호환성 검사 결과

### 렌더링 모드 확인
| 앱 | 모드 | 상태 |
|-----|------|------|
| apps/tools | SSG | ✅ |
| apps/sound-blue | SSR | ✅ |
| apps/dialogue | SPA | ❌ 수정 필요 |

### 위반 사항
| 파일 | 라인 | 위반 내용 |
|------|------|----------|
| ... | ... | ... |

### 통과 항목
- [x] core/ 브라우저 API 미사용
- [x] platform/ 이중 구현 완료
- [x] apps/ SEO 호환 렌더링
```

## SPA 금지 이유 (SEO 치명적 영향)

```text
╔══════════════════════════════════════════════════════════════════════════════╗
║                    🚨 SPA 금지 - SEO 치명적 영향 🚨                             ║
╠══════════════════════════════════════════════════════════════════════════════╣
║  • 초기 HTML이 비어있어 크롤러가 콘텐츠를 인식 못함                               ║
║  • Google도 JS 렌더링 큐를 별도로 거쳐 색인이 지연됨                             ║
║  • Bing, Naver 등은 JS 렌더링 지원이 제한적/불가                                ║
╚══════════════════════════════════════════════════════════════════════════════╝
```

## 허용 모드

| 모드 | 설명 | SEO |
|------|------|:---:|
| SSG | 빌드 시 HTML 생성 | ✅ |
| SSR | 요청 시 HTML 생성 | ✅ |
| SPA | 클라이언트 JS 렌더링 | ❌ |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/soundbluemusic) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
