---
name: i18n-checker
description: | Use when this capability is needed.
metadata:
  author: guny524
---

# i18n Checker

i18n 키 관리를 "빼먹지 마라"는 수동 검토 대신 스크립트 자동화로 해결.

## 문제 상황

- `en.json`에만 키를 정의하고 다른 언어 파일에는 누락
- 존재하지 않는 키를 코드에서 참조
- 사용하지 않는 키를 방치
- 동적 키 생성 시 보간자 불일치

## 해결 접근법

3겹의 안전장치:

1. **TypeScript 타입 검사**: 사용부/정의부 오류 컴파일 타임 감지
2. **미사용 키 린트 스크립트**: 코드베이스와 정의 간 불일치 발견
3. **pre-push 훅 자동화**: 푸시 전 `tsc` + `lint:i18n` 자동 실행

## 검증 스크립트

모든 스크립트는 `~/llms/skills/i18n-checker/scripts/`에 있으며 어느 프로젝트에서든 사용 가능.

### 미사용 키 감지

```bash
~/llms/skills/i18n-checker/scripts/find-unused-keys.sh
```

옵션:
- `-b, --base <file>`: 기준 언어 파일 (기본: 자동 탐색)
- `-s, --src <dir>`: 소스 코드 디렉토리 (기본: src)
- `-e, --ext <extensions>`: 검색할 확장자 (기본: ts,tsx,js,jsx)
- `-i, --ignore <prefixes>`: 무시할 prefix (콤마 구분)

예시:
```bash
# 기본 사용
~/llms/skills/i18n-checker/scripts/find-unused-keys.sh

# 동적 키 예외 처리
~/llms/skills/i18n-checker/scripts/find-unused-keys.sh -i 'faq.,errors.code_,dynamic.'

# 특정 파일/디렉토리 지정
~/llms/skills/i18n-checker/scripts/find-unused-keys.sh -b locales/en.json -s app
```

### 누락 키 감지

```bash
~/llms/skills/i18n-checker/scripts/find-missing-keys.sh
```

옵션:
- `-b, --base <file>`: 기준 언어 파일
- `--fix`: 초과 키 자동 제거

예시:
```bash
# 기본 사용
~/llms/skills/i18n-checker/scripts/find-missing-keys.sh

# 초과 키 자동 제거
~/llms/skills/i18n-checker/scripts/find-missing-keys.sh --fix
```

### 코드 미정의 키 감지

```bash
~/llms/skills/i18n-checker/scripts/find-undefined-keys.sh
```

옵션:
- `-b, --base <file>`: 기준 언어 파일
- `-s, --src <dir>`: 소스 코드 디렉토리
- `-e, --ext <extensions>`: 검색할 확장자

## TypeScript 타입 안정성

### next-intl (Next.js)

```typescript
// src/i18n/request.ts
import { getRequestConfig } from 'next-intl/server';

type Messages = typeof import('../../messages/en.json');

declare module 'next-intl' {
  interface IntlMessages extends Messages {}
}
```

### react-i18next (React)

```typescript
// src/i18n/types.d.ts
import 'react-i18next';
import en from '../locales/en.json';

declare module 'react-i18next' {
  interface CustomTypeOptions {
    defaultNS: 'translation';
    resources: {
      translation: typeof en;
    };
  }
}
```

## pre-push 훅 설정

```bash
# .husky/pre-push
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

npx tsc --noEmit
~/llms/skills/i18n-checker/scripts/find-unused-keys.sh
~/llms/skills/i18n-checker/scripts/find-missing-keys.sh
~/llms/skills/i18n-checker/scripts/find-undefined-keys.sh
```

또는 package.json 스크립트로 래핑:

```json
{
  "scripts": {
    "lint:i18n:unused": "~/llms/skills/i18n-checker/scripts/find-unused-keys.sh",
    "lint:i18n:missing": "~/llms/skills/i18n-checker/scripts/find-missing-keys.sh",
    "lint:i18n:undefined": "~/llms/skills/i18n-checker/scripts/find-undefined-keys.sh",
    "lint:i18n": "npm run lint:i18n:unused && npm run lint:i18n:missing && npm run lint:i18n:undefined"
  }
}
```

## 동적 키 예외 처리

FAQ, 동적 콘텐츠 등 패턴 기반 키는 `-i` 옵션으로 예외 처리:

```bash
~/llms/skills/i18n-checker/scripts/find-unused-keys.sh -i 'faq.,errors.code_,dynamic.'
```

## 의존성

- `jq`: JSON 파싱용 (설치: `brew install jq`)
- `grep`: 소스 코드 검색용 (macOS 기본 포함)

## 체크리스트

- [ ] jq 설치 확인 (`jq --version`)
- [ ] 기준 언어 파일 존재 (en.json 등)
- [ ] TypeScript 타입 정의 완료
- [ ] find-unused-keys.sh 실행 성공
- [ ] find-missing-keys.sh 실행 성공
- [ ] find-undefined-keys.sh 실행 성공
- [ ] 동적 키 예외 설정 (-i 옵션)
- [ ] pre-push 훅 설정 완료

## References

- [next-intl 설정](references/next-intl.md)
- [react-i18next 설정](references/react-i18next.md)
- [스크립트 상세 설명](references/scripts.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/guny524) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
