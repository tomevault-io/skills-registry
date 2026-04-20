---
name: code-cleaner
description: ESLint로 불필요한 코드 자동 제거 - unused imports, console.log, 사용하지 않는 변수 정리 Use when this capability is needed.
metadata:
  author: gdm0714
---

# 코드 클리너 스킬

코드베이스에서 불필요한 import, console.log, 사용하지 않는 변수를 자동으로 제거하여 깔끔한 코드를 유지합니다.

## 1. 개요

프로젝트가 커지면서 쌓이는 불필요한 코드를 자동으로 정리합니다.

### 해결하는 문제
- 사용하지 않는 import 문 수백 개
- 디버깅 후 남은 console.log
- Dead code와 사용하지 않는 변수
- 코드 리뷰 시 반복되는 지적사항

### ⏱️ Before vs After
- **Before**: 수동으로 파일마다 찾아서 제거 (2-3시간)
- **After**: `npm run lint:fix` 명령어 한 번 (10초)

## 2. 주요 기능

- ✅ 사용하지 않는 import 자동 제거
- ✅ console.log/warn/error 제거
- ✅ 사용하지 않는 변수 탐지 및 제거
- ✅ 중복 import 정리
- ✅ Git hook으로 커밋 전 자동 실행
- ✅ VS Code 저장 시 자동 정리

## 3. 설치 및 설정

### 패키지 설치

```bash
# ESLint 및 플러그인
npm install -D eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin
npm install -D eslint-plugin-unused-imports
npm install -D prettier eslint-config-prettier eslint-plugin-prettier

# Git hooks (선택)
npm install -D husky lint-staged
```

## 4. 기본 설정

### ESLint 설정 파일

```javascript
// .eslintrc.js
module.exports = {
  root: true,
  parser: '@typescript-eslint/parser',
  parserOptions: {
    ecmaVersion: 2022,
    sourceType: 'module',
    ecmaFeatures: {
      jsx: true,
    },
    project: './tsconfig.json',
  },
  env: {
    browser: true,
    es2022: true,
    node: true,
  },
  extends: [
    'eslint:recommended',
    'plugin:@typescript-eslint/recommended',
    'plugin:react/recommended',
    'plugin:react-hooks/recommended',
    'prettier',
  ],
  plugins: [
    '@typescript-eslint',
    'react',
    'react-hooks',
    'unused-imports',
    'prettier',
  ],
  rules: {
    // 사용하지 않는 import 제거
    'no-unused-vars': 'off',
    '@typescript-eslint/no-unused-vars': 'off',
    'unused-imports/no-unused-imports': 'error',
    'unused-imports/no-unused-vars': [
      'warn',
      {
        vars: 'all',
        varsIgnorePattern: '^_',
        args: 'after-used',
        argsIgnorePattern: '^_',
      },
    ],

    // console.log 금지 (production)
    'no-console': process.env.NODE_ENV === 'production' ? 'error' : 'warn',

    // 코드 품질
    'prefer-const': 'error',
    'no-var': 'error',
    'object-shorthand': 'error',
    'quote-props': ['error', 'as-needed'],

    // React 관련
    'react/react-in-jsx-scope': 'off', // Next.js는 불필요
    'react/prop-types': 'off', // TypeScript 사용
    'react/self-closing-comp': 'error',

    // Prettier 연동
    'prettier/prettier': 'error',
  },
  settings: {
    react: {
      version: 'detect',
    },
  },
  ignorePatterns: [
    'node_modules/',
    '.next/',
    'out/',
    'dist/',
    'build/',
    '*.config.js',
  ],
};
```

### Prettier 설정

```javascript
// .prettierrc.js
module.exports = {
  semi: true,
  trailingComma: 'es5',
  singleQuote: true,
  printWidth: 80,
  tabWidth: 2,
  useTabs: false,
  arrowParens: 'always',
  endOfLine: 'lf',
};
```

### package.json 스크립트

```json
{
  "scripts": {
    "lint": "eslint . --ext .js,.jsx,.ts,.tsx",
    "lint:fix": "eslint . --ext .js,.jsx,.ts,.tsx --fix",
    "format": "prettier --write \"**/*.{js,jsx,ts,tsx,json,css,md}\"",
    "type-check": "tsc --noEmit",
    "clean": "npm run lint:fix && npm run format"
  }
}
```

## 5. 핵심 기능 구현

### 5.1 자동 정리 스크립트

```typescript
// scripts/clean-code.ts
import { execSync } from 'child_process';
import * as fs from 'fs';
import * as path from 'path';

interface CleanOptions {
  removeConsole?: boolean;
  removeUnusedImports?: boolean;
  formatCode?: boolean;
}

/**
 * 코드베이스 정리 스크립트
 */
export async function cleanCodebase(options: CleanOptions = {}) {
  const {
    removeConsole = true,
    removeUnusedImports = true,
    formatCode = true,
  } = options;

  console.log('🧹 코드 정리 시작...');

  try {
    // 1. ESLint로 자동 수정
    if (removeUnusedImports) {
      console.log('📦 사용하지 않는 import 제거 중...');
      execSync('npm run lint:fix', { stdio: 'inherit' });
    }

    // 2. console.log 제거 (production)
    if (removeConsole && process.env.NODE_ENV === 'production') {
      console.log('🚫 console.log 제거 중...');
      await removeConsoleLogs('./src');
    }

    // 3. 코드 포맷팅
    if (formatCode) {
      console.log('✨ 코드 포맷팅 중...');
      execSync('npm run format', { stdio: 'inherit' });
    }

    // 4. 타입 체크
    console.log('🔍 타입 체크 중...');
    execSync('npm run type-check', { stdio: 'inherit' });

    console.log('✅ 코드 정리 완료!');
  } catch (error) {
    console.error('❌ 에러 발생:', error);
    process.exit(1);
  }
}

/**
 * console.log 제거 (재귀적)
 */
async function removeConsoleLogs(dir: string) {
  const files = fs.readdirSync(dir);

  for (const file of files) {
    const filePath = path.join(dir, file);
    const stat = fs.statSync(filePath);

    if (stat.isDirectory()) {
      // 제외할 디렉토리
      if (['node_modules', '.next', 'dist'].includes(file)) {
        continue;
      }
      await removeConsoleLogs(filePath);
    } else if (
      file.endsWith('.ts') ||
      file.endsWith('.tsx') ||
      file.endsWith('.js') ||
      file.endsWith('.jsx')
    ) {
      let content = fs.readFileSync(filePath, 'utf-8');
      const original = content;

      // console.log, console.warn, console.error 제거
      // 단, console.error는 에러 핸들링에서 유지할 수도 있음
      content = content.replace(
        /console\.(log|warn|debug|info)\([^)]*\);?\n?/g,
        ''
      );

      if (content !== original) {
        fs.writeFileSync(filePath, content, 'utf-8');
        console.log(`  ✓ ${filePath}`);
      }
    }
  }
}

// CLI 실행
if (require.main === module) {
  cleanCodebase({
    removeConsole: process.env.NODE_ENV === 'production',
    removeUnusedImports: true,
    formatCode: true,
  });
}
```

### 5.2 Git Hook 설정 (커밋 전 자동 정리)

```bash
# Husky 초기화
npx husky-init && npm install
```

```javascript
// .husky/pre-commit
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

npx lint-staged
```

```json
// package.json에 추가
{
  "lint-staged": {
    "*.{js,jsx,ts,tsx}": [
      "eslint --fix",
      "prettier --write"
    ],
    "*.{json,css,md}": [
      "prettier --write"
    ]
  }
}
```

### 5.3 VS Code 설정 (저장 시 자동 정리)

```json
// .vscode/settings.json
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true,
    "source.organizeImports": true
  },
  "eslint.validate": [
    "javascript",
    "javascriptreact",
    "typescript",
    "typescriptreact"
  ],
  "[typescript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[typescriptreact]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  }
}
```

### 5.4 커스텀 ESLint 플러그인

```typescript
// eslint-rules/no-magic-numbers.ts
/**
 * 매직 넘버 방지 (선택적)
 */
export const noMagicNumbers = {
  meta: {
    type: 'suggestion',
    docs: {
      description: '매직 넘버 대신 상수 사용 권장',
    },
  },
  create(context: any) {
    return {
      Literal(node: any) {
        if (typeof node.value === 'number' && node.value !== 0 && node.value !== 1) {
          context.report({
            node,
            message: '매직 넘버를 상수로 추출하세요',
          });
        }
      },
    };
  },
};
```

## 6. 실전 예제

### Before: 정리 전 코드

```typescript
// components/UserProfile.tsx
import React, { useState, useEffect } from 'react';
import { User } from '@/types/user';
import { formatDate } from '@/utils/date';
import { validateEmail } from '@/utils/validation'; // 사용 안 함
import axios from 'axios'; // 사용 안 함
import { Button } from './Button'; // 사용 안 함

export function UserProfile({ userId }: { userId: string }) {
  const [user, setUser] = useState<User | null>(null);
  const [loading, setLoading] = useState(true);
  const unusedVar = 'test'; // 사용 안 함

  useEffect(() => {
    console.log('Fetching user:', userId);
    fetchUser();
  }, [userId]);

  async function fetchUser() {
    console.log('API 호출 시작');
    try {
      const response = await fetch(`/api/users/${userId}`);
      const data = await response.json();
      console.log('받은 데이터:', data);
      setUser(data);
    } catch (error) {
      console.error('에러:', error);
    } finally {
      setLoading(false);
      console.log('로딩 완료');
    }
  }

  if (loading) return <div>Loading...</div>;

  return (
    <div>
      <h1>{user?.name}</h1>
      <p>{formatDate(user?.createdAt)}</p>
    </div>
  );
}
```

### After: 정리 후 코드

```typescript
// components/UserProfile.tsx
import React, { useState, useEffect } from 'react';
import { User } from '@/types/user';
import { formatDate } from '@/utils/date';

export function UserProfile({ userId }: { userId: string }) {
  const [user, setUser] = useState<User | null>(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetchUser();
  }, [userId]);

  async function fetchUser() {
    try {
      const response = await fetch(`/api/users/${userId}`);
      const data = await response.json();
      setUser(data);
    } catch (error) {
      console.error('에러:', error); // 에러는 유지
    } finally {
      setLoading(false);
    }
  }

  if (loading) return <div>Loading...</div>;

  return (
    <div>
      <h1>{user?.name}</h1>
      <p>{formatDate(user?.createdAt)}</p>
    </div>
  );
}
```

### 실행 결과

```bash
$ npm run clean

🧹 코드 정리 시작...
📦 사용하지 않는 import 제거 중...
  ✓ components/UserProfile.tsx (3개 import 제거)
  ✓ utils/api.ts (1개 import 제거)
🚫 console.log 제거 중...
  ✓ components/UserProfile.tsx (4개 제거)
  ✓ pages/dashboard.tsx (2개 제거)
✨ 코드 포맷팅 중...
  ✓ 15개 파일 포맷팅 완료
🔍 타입 체크 중...
  ✓ 타입 에러 없음
✅ 코드 정리 완료!
```

## 7. 환경 변수

```bash
# .env.local
NODE_ENV=development # production에서만 console.log 제거

# ESLint 캐시 사용 (빠른 실행)
ESLINT_USE_FLAT_CONFIG=false
```

## 8. 베스트 프랙티스

### ✅ 권장사항

```typescript
// 1. 의미 있는 변수명 사용
const MAX_RETRY_COUNT = 3; // ✅
const n = 3; // ❌

// 2. console.error는 에러 핸들링에서 유지
try {
  await dangerousOperation();
} catch (error) {
  console.error('Critical error:', error); // ✅ 유지
  // 또는 에러 로깅 서비스 사용
  logger.error('Critical error:', error);
}

// 3. 개발 모드에서만 console 사용
if (process.env.NODE_ENV === 'development') {
  console.log('Debug info:', data); // ✅
}

// 4. import 그룹화 (ESLint plugin-import 사용 시)
// 1) React 관련
// 2) 외부 라이브러리
// 3) 내부 모듈
// 4) 타입
import React from 'react';
import { NextPage } from 'next';
import { Button } from '@/components/Button';
import type { User } from '@/types/user';
```

### ❌ 비권장사항

```typescript
// 1. 주석으로 ESLint 무시 남발
/* eslint-disable */ // ❌ 파일 전체 비활성화
const unused = 'variable'; // eslint-disable-line // ❌ 남발하지 말 것

// 2. console.log로 디버깅 후 제거 안 함
console.log('TODO: 나중에 지우기'); // ❌

// 3. 사용하지 않는 변수를 _ 로 시작하지 않음
const unusedData = fetchData(); // ❌
const _unusedData = fetchData(); // ✅ (경고 안 뜸)

// 4. any 타입 남발
const data: any = response; // ❌
const data: UserResponse = response; // ✅
```

### 💡 팁

```typescript
// 1. Debug용 헬퍼 함수 사용
export function debugLog(...args: any[]) {
  if (process.env.NODE_ENV === 'development') {
    console.log('[DEBUG]', ...args);
  }
}

// 2. 임시 변수는 _ 접두사 사용
const _tempData = processData(); // 나중에 사용 예정

// 3. ESLint 규칙 팀 단위 커스터마이징
// .eslintrc.js에서 팀 컨벤션 반영

// 4. pre-commit hook으로 자동화
// 커밋 시 자동으로 정리되므로 신경 쓸 필요 없음
```

## 9. 트러블슈팅

### 문제 1: ESLint가 너무 느림

```bash
# 해결: 캐시 사용
npm run lint -- --cache

# 또는 .eslintrc.js에 추가
module.exports = {
  cache: true,
  cacheLocation: 'node_modules/.cache/eslint',
};
```

### 문제 2: Prettier와 ESLint 충돌

```bash
# 해결: eslint-config-prettier 설치 및 설정
npm install -D eslint-config-prettier

# .eslintrc.js의 extends 마지막에 추가
extends: [
  'eslint:recommended',
  'prettier', // 마지막에 위치
],
```

### 문제 3: import 순서 정렬 안 됨

```bash
# 해결: eslint-plugin-import 설치
npm install -D eslint-plugin-import

# .eslintrc.js에 추가
plugins: ['import'],
rules: {
  'import/order': [
    'error',
    {
      groups: [
        'builtin',
        'external',
        'internal',
        'parent',
        'sibling',
        'index',
      ],
      'newlines-between': 'always',
    },
  ],
}
```

### 문제 4: 특정 파일만 제외하고 싶음

```javascript
// .eslintrc.js
module.exports = {
  ignorePatterns: [
    'node_modules/',
    '.next/',
    'public/',
    '*.config.js',
    'src/legacy/**', // legacy 코드 제외
  ],
};
```

### 문제 5: Git hook이 너무 느림

```json
// package.json - 변경된 파일만 체크
{
  "lint-staged": {
    "*.{js,jsx,ts,tsx}": [
      "eslint --fix --max-warnings=0"
    ]
  }
}

// 또는 .lintstagedrc.js
module.exports = {
  '*.{js,jsx,ts,tsx}': (filenames) =>
    `eslint --fix --max-warnings=0 ${filenames.join(' ')}`,
};
```

## 10. 참고 자료

### 공식 문서
- [ESLint 공식 문서](https://eslint.org/docs/latest/)
- [TypeScript ESLint](https://typescript-eslint.io/)
- [Prettier](https://prettier.io/docs/en/index.html)
- [eslint-plugin-unused-imports](https://github.com/sweepline/eslint-plugin-unused-imports)

### 추가 플러그인
- [eslint-plugin-import](https://github.com/import-js/eslint-plugin-import) - import 순서 정렬
- [eslint-plugin-react-hooks](https://www.npmjs.com/package/eslint-plugin-react-hooks) - React Hooks 규칙
- [eslint-plugin-jsx-a11y](https://github.com/jsx-eslint/eslint-plugin-jsx-a11y) - 접근성 체크

### 참고 설정
- [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript)
- [Google TypeScript Style Guide](https://google.github.io/styleguide/tsguide.html)
- [Next.js ESLint 설정](https://nextjs.org/docs/app/building-your-application/configuring/eslint)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gdm0714) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
