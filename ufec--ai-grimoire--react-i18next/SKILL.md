---
name: react-i18next
description: > Use when this capability is needed.
metadata:
  author: ufec
---

# react-i18next Best Practices Skill

## Read Before Using

Read the corresponding reference files as needed:

| Scenario | Reference File |
|----------|---------------|
| All projects (required) | `references/core-setup.md` |
| 10+ pages (large-scale projects) | `references/namespace-design.md` |
| TypeScript projects | `references/typescript-integration.md` |
| Translation file conventions | `references/translation-conventions.md` |
| AI auto-translation workflow | `references/ai-translation.md` |

---

## Quick Decision Flow

```
Does the user's project have > 10 pages?
  ├── Yes → Use domain-based namespace architecture (see namespace-design.md)
  └── No  → A single namespace is sufficient (see core-setup.md)

Is TypeScript being used?
  └── Yes → Must configure type safety (see typescript-integration.md)

Is AI auto-translation to multiple languages needed?
  └── Yes → See ai-translation.md (workflow + prompt templates + CI integration)

Are there translation convention questions (key naming, nesting depth)?
  └── See translation-conventions.md
```

---

## Core Principles (Applicable to All Projects)

1. **Namespace = Domain Boundary** — align with feature modules, not individual pages
2. **`common` namespace is always the default NS** — stores cross-module reusable copy
3. **Semantic keys** — use nested structures; names like `text1` or `label_01` are prohibited
4. **Interpolation over concatenation** — always use `{{variable}}` instead of string concatenation
5. **TypeScript projects must configure `CustomTypeOptions`** — enables compile-time type checking
6. **English (`en`) is the source of truth** — maintained manually; all other languages are AI-translated from English

---

## Recommended Directory Structure

### Large-Scale Project (20+ pages, currently applicable)

```
src/
├── i18n/
│   ├── index.ts                    # Initialization entry point
│   ├── types.ts                    # TypeScript type declarations
│   ├── hooks/
│   │   └── useAppTranslation.ts    # Wraps useTranslation with unified NS management
│   └── locales/
│       ├── en/                     # ✅ English (sole source of truth, manually maintained)
│       │   ├── common.json
│       │   ├── auth.json
│       │   ├── home.json
│       │   ├── profile.json
│       │   ├── order.json
│       │   └── settings.json
│       ├── zh/                     # 🤖 AI-translated, do not edit manually
│       ├── ja/                     # 🤖 AI-translated
│       ├── ko/                     # 🤖 AI-translated
│       └── fr/                     # 🤖 AI-translated
│
scripts/
├── translate.ts                    # AI translation script (see ai-translation.md)
└── check-translations.ts           # Translation completeness checker
```

**Namespace Partitioning Principle**: Partition by **business domain**; one domain serves multiple pages:
- `auth` → Login page, Registration page, Forgot Password page (3 pages sharing one NS)
- `order` → Order List page, Order Detail page, Checkout page (N pages sharing one NS)
- `profile` → Profile page, Avatar Edit page (shared)

---

## Reference File Index

- **`references/core-setup.md`** — Full initialization code, React Native language detection, lazy-loading configuration
- **`references/namespace-design.md`** — Namespace design strategy for 20+ page projects, domain mapping table
- **`references/typescript-integration.md`** — `CustomTypeOptions` configuration, type-safe hook wrappers
- **`references/translation-conventions.md`** — JSON structure conventions, key naming rules, pluralization / interpolation usage
- **`references/ai-translation.md`** — AI translation workflow, prompt templates, incremental translation, CI integration

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ufec) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
