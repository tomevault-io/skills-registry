---
name: frontend-quality-linting
description: Standardized guidelines and patterns for Frontend Quality Linting. Use when this capability is needed.
metadata:
  author: valec3
---

# Frontend Quality Linting

## When to use this skill
- Setting up ESLint
- Enforcing code quality
- Catching bugs early

## Workflow
- [ ] Install ESLint
- [ ] Configure for TypeScript/React
- [ ] Add plugins (react-hooks, etc.)
- [ ] Set up pre-commit hooks
- [ ] Run in CI/CD

## Instructions

### Installation
```bash
npm install -D eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin
npm install -D eslint-plugin-react eslint-plugin-react-hooks
```

### Config
```javascript
// .eslintrc.js
module.exports = {
  parser: '@typescript-eslint/parser',
  plugins: ['@typescript-eslint', 'react', 'react-hooks'],
  extends: [
    'eslint:recommended',
    'plugin:@typescript-eslint/recommended',
    'plugin:react/recommended',
    'plugin:react-hooks/recommended'
  ],
  rules: {
    'react-hooks/rules-of-hooks': 'error',
    'react-hooks/exhaustive-deps': 'warn'
  }
};
```

## Resources
- Enable react-hooks rules
- Add to pre-commit with husky
- Auto-fix on save in editor

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/valec3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
