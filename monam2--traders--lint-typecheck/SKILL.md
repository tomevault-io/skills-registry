---
name: lint-typecheck
description: ESLint 및 TypeScript 정적 분석 스킬 Use when this capability is needed.
metadata:
  author: monam2
---

# Lint & TypeCheck 스킬

## 명령어

### Lint 실행

```bash
pnpm lint
```

### Lint 자동 수정

```bash
pnpm lint --fix
```

### TypeScript 타입 검사

```bash
pnpm typecheck
# 또는
tsc --noEmit
```

## ESLint 설정

### .eslintrc.json

```json
{
  "extends": ["next/core-web-vitals", "next/typescript", "prettier"],
  "rules": {
    "@typescript-eslint/no-unused-vars": "error",
    "@typescript-eslint/no-explicit-any": "warn",
    "prefer-const": "error"
  }
}
```

## Prettier 설정

### .prettierrc

```json
{
  "semi": true,
  "singleQuote": true,
  "tabWidth": 2,
  "trailingComma": "es5",
  "printWidth": 100
}
```

## package.json 스크립트

```json
{
  "scripts": {
    "lint": "next lint",
    "lint:fix": "next lint --fix",
    "typecheck": "tsc --noEmit",
    "format": "prettier --write ."
  }
}
```

## CI 통합

GitHub Actions에서 PR마다 실행:

```yaml
- run: pnpm lint
- run: pnpm typecheck
```

## 자주 발생하는 에러

### 1. unused-vars

```typescript
// ❌
const unused = "never used";

// ✅ 의도적 무시
const _intentionallyUnused = "documented";
```

### 2. no-explicit-any

```typescript
// ❌
function process(data: any) {}

// ✅
function process(data: unknown) {}
function process(data: AnalysisResult) {}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/monam2) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
