---
name: lint
description: IDE 진단, tsc, ESLint, Java 빌드 에러 등 lint 오류를 수집하고 수정합니다. Use when this capability is needed.
metadata:
  author: nabi-chan
---

# Lint/빌드 에러 수집 및 수정

## 절차

1. **진단 수집**
   - `mcp__ide__getDiagnostics`로 IDE 진단 정보 수집 (사용 가능한 경우)
   - 프로젝트 타입에 따라 CLI 린트 명령 실행:
     - TypeScript: `npx tsc --noEmit` (tsconfig.json 존재 시)
     - Node: `npx eslint . --format json` (eslint 설정 존재 시)
     - Java: `./gradlew build` 또는 `mvn compile` (빌드 파일 존재 시)
   - 수집된 에러를 파일별로 그룹화하여 사용자에게 요약 표시

2. **수정**
   - 에러를 파일 단위로 순차 수정
   - 한 파일의 모든 에러를 한 번에 수정
   - 자동 수정 가능한 경우 CLI 활용 (예: `eslint --fix`)

3. **검증**
   - 수정 후 동일한 진단을 재실행하여 해결 여부 확인
   - 최대 3회 반복 (수정 → 검증 사이클)
   - 해결되지 않은 에러는 목록으로 보고

## 주의사항

- 사용자가 명시적으로 `/lint`를 호출한 경우에만 실행
- 원래 코드의 의도를 변경하지 않고 최소한으로 수정
- cSpell(맞춤법) 경고는 무시하거나, 사용자에게 확인 후 처리
- 빌드 설정 파일(build.gradle, pom.xml, tsconfig.json 등)은 수정하지 않음

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nabi-chan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
