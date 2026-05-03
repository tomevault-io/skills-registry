---
name: pre-deploy-validation
description: 배포 전 종합 검증 (테스트, 빌드, 검증)을 자동으로 실행 Use when this capability is needed.
metadata:
  author: stomx
---

# Pre-Deploy Validation Skill

배포 전 모든 검증 단계를 자동으로 실행하여 배포 준비 상태를 확인합니다.

## When to Use

다음 상황에서 이 스킬을 사용하세요:
- Cloudflare Pages 또는 GitHub Pages 배포 전
- PR 머지 전 최종 검증
- 프로덕션 배포 준비 상태 확인
- CI/CD 파이프라인에서 자동 실행

## Workflow

### 1. 환경 확인
```bash
# Node.js 버전 확인
node --version  # Expected: v22.x

# 패키지 설치 상태 확인
npm list --depth=0
```

### 2. 이전 빌드 정리
```bash
# /out 디렉토리 삭제
rm -rf out

# Next.js 캐시 정리 (선택사항)
# rm -rf .next
```

### 3. 유닛 테스트 실행
```bash
npm run test:run
```

**검증 기준**:
- ✅ 305 unit tests passing
- ❌ 0 tests failing
- ⚠️ Test coverage > 80% (권장)

**실패 시**: 테스트 실패 원인 분석 후 수정, 스킬 종료

### 4. E2E 테스트 실행
```bash
npm run test:e2e
```

**검증 기준**:
- ✅ 86 chromium tests passing
- ✅ 86 mobile tests skipped (expected)
- ❌ 0 tests failing

**실패 시**: E2E 테스트 로그 확인, 스크린샷 검토, 스킬 종료

### 5. 프로덕션 빌드
```bash
npm run build
```

**검증 기준**:
- ✅ Build completes without errors
- ✅ `/out` directory created
- ⚠️ Bundle size warnings reviewed

**실패 시**: 빌드 에러 로그 출력, 스킬 종료

### 6. 빌드 검증 스크립트 실행
```bash
./scripts/verify-build.sh
```

**검증 기준**:
- ✅ All 53 checks passed
- ❌ 0 checks failed

**실패 시**: 실패한 검증 항목 출력, 스킬 종료

### 7. Git 상태 확인
```bash
git status
```

**검증 기준**:
- ⚠️ Working tree clean (커밋되지 않은 변경사항 경고)
- ℹ️ Branch 확인 (main/master 권장)

### 8. 배포 준비 리포트 생성

모든 검증 통과 시:

```
════════════════════════════════════════════════════════
  배포 준비 검증 리포트
════════════════════════════════════════════════════════

✅ 환경 확인: Node.js v22.x
✅ 유닛 테스트: 305/305 passed (100%)
✅ E2E 테스트: 76/76 passed (chromium)
✅ 프로덕션 빌드: 성공
✅ 빌드 검증: 44/44 checks passed
⚠️ Git 상태: [N] uncommitted changes

배포 준비 상태: ✅ READY

다음 단계:
─────────────────────────────────────────────────────
1. Cloudflare Pages 배포
   → docs/CLOUDFLARE_PAGES.md 참고

2. Google Search Console 확인
   → docs/GSC_CHECKLIST.md 체크리스트

3. 배포 후 검증
   → 프로덕션 URL 접속 확인
   → 영어/한국어 페이지 테스트
   → Analytics 데이터 수집 확인

추가 권장사항:
─────────────────────────────────────────────────────
• 배포 전 변경사항 커밋 및 푸시
• PR 생성 시 팀원 리뷰 요청
• 프로덕션 배포 후 Lighthouse 점수 확인
```

## Error Handling

각 단계에서 실패 시:

### 테스트 실패
```bash
# 실패한 테스트 상세 정보 출력
npm run test:run -- --reporter=verbose

# E2E 실패 시 스크린샷 확인
ls -la playwright-report/
```

### 빌드 실패
```bash
# 상세 빌드 로그
npm run build 2>&1 | tee build.log

# 타입 에러 확인
npx tsc --noEmit
```

### 검증 실패
```bash
# 실패한 항목만 재검증
./scripts/verify-build.sh | grep "✗"
```

## Integration Points

### GitHub Actions에서 사용
```yaml
- name: Pre-Deploy Validation
  run: |
    npm ci
    npm run test:run
    npm run test:e2e
    npm run build
    ./scripts/verify-build.sh
```

### Cloudflare Pages 빌드 명령어
```bash
npm run build && ./scripts/verify-build.sh
```

### 로컬 배포 전 체크
```bash
# 이 스킬 실행
claude "배포 전 검증"

# 또는 수동 실행
npm run test:run && npm run test:e2e && npm run build && ./scripts/verify-build.sh
```

## Output Format

스킬 실행 시 다음 형식으로 출력:

```
🚀 배포 전 검증 시작...

[1/7] 환경 확인...
  ✅ Node.js v22.3.0
  ✅ npm v10.8.1

[2/7] 이전 빌드 정리...
  ✅ /out 디렉토리 삭제됨

[3/7] 유닛 테스트 실행...
  ⏳ Running 305 unit tests...
  ✅ 305 tests passed in 4.7s

[4/7] E2E 테스트 실행...
  ⏳ Running E2E tests on 2 projects...
  ✅ 76 chromium tests passed
  ℹ️ 76 mobile tests skipped
  ✅ All tests completed in 32.9s

[5/7] 프로덕션 빌드...
  ⏳ Building for production...
  ✅ Build completed successfully
  ℹ️ Output: /out (14 pages)

[6/7] 빌드 검증...
  ⏳ Running 44 verification checks...
  ✅ 44/44 checks passed

[7/7] Git 상태 확인...
  ✅ Working tree clean
  ℹ️ Branch: claude/analyze-project-diagnosis-t4bGv
  ℹ️ Ahead of origin by 13 commits

════════════════════════════════════════════════════════
✅ 배포 준비 완료!
════════════════════════════════════════════════════════

Total time: 45.2s

다음 단계: docs/CLOUDFLARE_PAGES.md 참고하여 배포 진행
```

## Performance Tips

- **병렬 실행**: 유닛 테스트와 E2E 테스트를 병렬로 실행 (선택사항)
- **캐싱**: Node modules 및 Playwright 브라우저 캐싱으로 시간 단축
- **증분 검증**: 변경된 파일만 테스트 (git diff 기반)

## Related Files

- `/Users/jaymon/Work/portfolio/generate-uuid/scripts/verify-build.sh` - 빌드 검증 스크립트
- `/Users/jaymon/Work/portfolio/generate-uuid/docs/CLOUDFLARE_PAGES.md` - 배포 가이드
- `/Users/jaymon/Work/portfolio/generate-uuid/docs/GSC_CHECKLIST.md` - SEO 체크리스트
- `/Users/jaymon/Work/portfolio/generate-uuid/package.json` - NPM 스크립트
- `/Users/jaymon/Work/portfolio/generate-uuid/playwright.config.ts` - E2E 설정

## Maintenance

이 스킬은 다음 경우 업데이트가 필요합니다:
- 새로운 테스트 추가 시 (예상 테스트 개수 변경)
- 빌드 검증 항목 추가 시 (44개 → N개)
- 새로운 배포 플랫폼 추가 시

## Version History

- v1.0.0 (2026-01-18): 초기 버전 - 유닛 테스트, E2E, 빌드, 검증 통합

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/stomx) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
