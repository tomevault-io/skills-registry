---
name: frontend-quality-formatting
description: Standardized guidelines and patterns for Frontend Quality Formatting. Use when this capability is needed.
metadata:
  author: valec3
---

# Frontend Quality Formatting

## When to use this skill
- Enforcing code style
- Setting up Prettier
- Configuring auto-formatting

## Workflow
- [ ] Install Prettier
- [ ] Create .prettierrc
- [ ] Add format script
- [ ] Set up editor integration
- [ ] Add to pre-commit

## Instructions

### Installation
```bash
npm install -D prettier
```

### Config
```json
// .prettierrc
{
  "semi": true,
  "singleQuote": true,
  "tabWidth": 2,
  "trailingComma": "es5"
}
```

### Scripts
```json
{
  "scripts": {
    "format": "prettier --write "src/**/*.{ts,tsx}""
  }
}
```

## Resources
- Consistent code style
- Auto-format on save
- Combine with ESLint

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/valec3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
