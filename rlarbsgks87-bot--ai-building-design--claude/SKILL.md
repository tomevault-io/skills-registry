---
name: ai-building-design
description: AI 건축 기획설계 서비스 개발 스킬. Flexity 벤치마킹 프로젝트로 Next.js + Tailwind + Three.js 기반. VWorld/공공데이터포털 API 연동 및 Google Gemini AI 통합. 사용 시점 - 토지분석 API 개발, AI 매스 스터디, 수익성 분석, 3D 시각화, Lambda 프록시 구현. 핵심 규칙 - URL 끝 슬래시 필수, 한글 파라미터는 POST+JSON, 타임아웃 설정, CORS 헤더. Use when this capability is needed.
metadata:
  author: rlarbsgks87-bot
---

# AI 건축 기획설계 서비스 개발 스킬

## 프로젝트 개요

- **벤치마킹**: Flexity (https://flexity.app/)
- **차별점**: 무료 + 광고수익 + 사용자 AI API 키
- **배포**: Vercel
- **프록시**: AWS Lambda (서울 리전)

## 기술 스택

- Next.js 14+ (App Router)
- TypeScript, Tailwind CSS
- Three.js / React-Three-Fiber (3D)
- Google Gemini API (사용자 키)
- VWorld, 공공데이터포털 API

---

## ⚠️ API 호출 필수 규칙

### 규칙 1: URL 끝 슬래시(/) 필수

```typescript
// ❌ 404 에러
const URL = 'https://xxx.execute-api.amazonaws.com/prod';

// ✅ 정상
const URL = 'https://xxx.execute-api.amazonaws.com/prod/';
```

### 규칙 2: 한글 파라미터는 POST + JSON

```typescript
// ❌ GET - 인코딩 깨짐
fetch(`${URL}?address=${encodeURIComponent('제주시')}`);

// ✅ POST - 안전
fetch(URL, {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ address: '제주시 도남동 50-11' })
});
```

### 규칙 3: Lambda GET/POST 모두 처리

```javascript
let params = event.queryStringParameters || {};
if (event.body) {
  params = { ...params, ...JSON.parse(event.body) };
}
```

### 규칙 4: CORS 헤더 필수

```javascript
const headers = {
  'Access-Control-Allow-Origin': '*',
  'Access-Control-Allow-Headers': 'Content-Type',
  'Access-Control-Allow-Methods': 'GET, POST, OPTIONS'
};
```

### 규칙 5: 타임아웃 설정

```typescript
fetch(url, { signal: AbortSignal.timeout(15000) });
```

### 규칙 6: 응답 검증 후 JSON 파싱

```typescript
if (!res.ok) throw new Error(`API Error: ${res.status}`);
const data = await res.json();
```

---

## 참조 문서

상세 정보는 references 폴더 참조:

- **API 상세 규칙**: references/api-rules.md
- **건축 기준**: references/building-standards.md  
- **AI 프롬프트**: references/ai-prompts.md

---

## 프로젝트 구조

```
/app
  /page.tsx                # 메인 페이지
  /layout.tsx              # 레이아웃 (AdSense)
  /api/analyze/route.ts    # 토지분석 API
/lib
  /constants.ts            # 건폐율/용적률
  /prompts.ts              # AI 프롬프트
/components
  /AddressInput.tsx
  /MassStudyResult.tsx
  /Building3DView.tsx
```

---

## Git 배포

```bash
git add -A && git commit -m "feat: 설명" && git push
```

## 디버깅

```bash
# Lambda POST 테스트
curl -X POST "https://xxx.amazonaws.com/prod/" \
  -H "Content-Type: application/json" \
  -d '{"type":"geocode","address":"제주시 도남동"}'
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rlarbsgks87-bot) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
