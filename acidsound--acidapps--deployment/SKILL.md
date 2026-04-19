---
name: deployment
description: Deployment preparation workflow including version management, documentation sync, and pre-deploy checklist for web audio apps. Use when this capability is needed.
metadata:
  author: acidsound
---

# 🚀 Deployment Skill

배포 전 준비 작업을 위한 체크리스트 및 자동화 도구.

## 🎯 용도

- 버전 번호 일관성 확인
- Service Worker 캐시 업데이트
- 문서 동기화 (영어/한국어)
- 변경 이력 업데이트

## 📋 체크리스트

### 1. 버전 일관성 확인

```bash
# Service Worker 버전 확인
grep -n "CACHE_NAME\|CACHE_VERSION" sw.js

# HTML 버전 표시 확인
grep -n "version\|versionDisplay" index.html

# package.json 버전 확인
cat package.json | grep '"version"'
```

**확인 사항**:
- [ ] sw.js의 `CACHE_NAME` 업데이트됨
- [ ] index.html의 버전 표시 업데이트됨
- [ ] package.json의 version 업데이트됨 (있는 경우)

### 2. Service Worker 에셋 확인

```bash
# sw.js에서 캐시할 파일 목록 확인
grep -A 50 "ASSETS\|urlsToCache" sw.js
```

**확인 사항**:
- [ ] 새로 추가된 JS 파일 포함됨
- [ ] 새로 추가된 CSS 파일 포함됨
- [ ] 새로 추가된 에셋(이미지, 폰트) 포함됨

### 3. 최근 변경 사항 검토

```bash
# 최근 10개 커밋 확인
git log --oneline -n 10

# 마지막 태그 이후 변경 확인
git log --oneline $(git describe --tags --abbrev=0)..HEAD
```

### 4. 문서 업데이트

**업데이트할 문서**:
- [ ] `README.md` - 기능 목록, 버전 정보
- [ ] `USER_MANUAL.md` - 새 기능 설명
- [ ] `CHANGELOG.md` - 변경 이력 추가
- [ ] `.agent/PROJECT_CONTEXT.md` - 아키텍처, 버전 정보

### 5. 다국어 문서 동기화

```bash
# 한국어 문서 확인
ls -la *_ko.md docs/*_ko.md 2>/dev/null
```

**확인 사항**:
- [ ] `USER_MANUAL.md` 변경 시 → `USER_MANUAL_ko.md` 동기화
- [ ] 섹션 구조 일치 확인

### 6. 스크린샷 갱신

새 UI 기능이 추가된 경우:

```bash
# 스크린샷 생성 스크립트 실행
node scripts/generate_screenshots.js
```

### 7. 최종 검증

```bash
# 빌드 테스트 (Vite 프로젝트)
npm run build

# 타입 체크 (TypeScript)
npx tsc --noEmit

# 린트 체크
npm run lint
```

### 8. 커밋 및 푸시

```bash
git add .
git commit -m "v{VERSION}: {변경 사항 요약}"
git push origin main

# 태그 추가 (선택)
git tag v{VERSION}
git push origin v{VERSION}
```

## 🔧 버전 번호 규칙

| 변경 유형 | 버전 증가 | 예시 |
|----------|----------|------|
| 버그 수정 | 패치 (+1) | v89 → v90 |
| 새 기능 | 마이너 (+1) | v89 → v90 |
| 대규모 변경 | 메이저 (+1) | v1.0 → v2.0 |

## 📝 커밋 메시지 형식

```
v{버전}: {제목}

- {상세 변경 1}
- {상세 변경 2}

Closes #{이슈번호} (있는 경우)
```

### 예시

```
v91: UI Refinement & Icon Restoration

- Reverted trash icon toggle behavior
- Unified button sizes (32x24px)
- Increased icon stroke-width (2.5px)
```

## 🔗 프로젝트별 특이사항

### acidBros
- 버전: `sw.js`의 `CACHE_NAME` + `index.html`의 `.version-display`
- 캐시: 모든 JS/CSS/폰트 수동 등록

### ddxx7
- 버전: `package.json` + Vite 빌드 해시
- 자동 캐시 무효화

### uss44
- 버전: `package.json`
- 자동 캐시 무효화

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/acidsound) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
