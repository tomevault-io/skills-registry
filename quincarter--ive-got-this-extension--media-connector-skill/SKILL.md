---
name: media-connector-skill
description: Guidelines and workflows for the Emby/Jellyfin Media Connector Chrome extension. Use this when developing components, content scripts, or background services to ensure adherence to strict architectural patterns, testing requirements, and the purple gradient aesthetic. Use when this capability is needed.
metadata:
  author: quincarter
---

# Media Connector Skill

## 🔋 Core Directives

- **Always** start every response with a 🔋.
- **Test Everything**: Write tests for every change. Test failures are clues.
- **Quality Gates**: Run `npm run lint`, `npm run format`, `npm run test`, and `npm run build` after every set of changes.
- **Coverage**: Maintain 90%+ test coverage.
- **Package Manager**: Use `yarn` to install packages.

## 🛠 Technology Stack

- **View Layer**: Lit (LitElement)
- **Build Tool**: Vite
- **Language**: TypeScript (Strict Mode)
- **Testing**: Web Test Runner (`wtr`) for components, Vitest for logic.

## 🏗 Architecture

### Component Structure

Every component lives in `src/components/[name]/`:

- `[name].ts`: Logic & Template.
- `[name].styles.ts`: Scoped CSS (using `css` literal).
- `[name].test.ts`: Component tests.

### Coding Standards

- **Mixins**: All components must extend `ComponentMixin(LitElement)`.
- **Styles**: No inline styles. Import from `.styles.ts`. Use `typography.css.ts` for text.
- **Types**: Strict typing. No `any`. Use discriminated unions instead of Enums.
- **Lit Best Practices**:
  - Decorators on their own line.
  - Explicit attribute mapping: `@property({ attribute: 'my-attr' })`.
  - Use `nothing` for conditional rendering (never `null`).

## 🎨 Aesthetic

- **Theme**: Purple gradient background.
- **Icons**: Use Emby/Jellyfin/Jellyseerr SVG icons from `src/assets`.

## 🧪 Testing Workflow

1. Implement feature/fix.
2. Add/Update tests in `*.test.ts` or `*.vitest.ts`.
3. Verify 90% coverage.
4. Run full suite: `yarn test && yarn build`.
5. Run lint: `yarn lint && yarn format`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/quincarter) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
