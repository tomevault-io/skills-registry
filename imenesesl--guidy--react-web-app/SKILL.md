---
name: react-web-app
description: >- Use when this capability is needed.
metadata:
  author: imenesesl
---

# React Web App

## Tech Stack

| Tool                    | Purpose                                    |
| ----------------------- | ------------------------------------------ |
| Rspack                  | Bundler                                    |
| React 19 (latest)       | UI library — always 19.x, never older      |
| TypeScript (strict)     | Language                                   |
| ESLint 10 (flat config) | Linting — always 10.x with @eslint/js 10.x |
| TanStack Router         | Navigation                                 |
| TanStack Query          | Server state / data fetching               |
| React Hook Form         | Form management                            |
| Valibot                 | Schema validation                          |
| Storybook               | Component docs, visual tests               |
| Vitest                  | Unit and integration tests                 |
| react-i18next           | Internationalization                       |

## Creating a New Web App

### Step 1 — Scaffold with Rspack

```bash
cd apps/web
npx @rspack/create <app-name>
```

Select the React + TypeScript template.

### Step 2 — Install core dependencies

IMPORTANT: Always use React 19 (latest) and ESLint 10 (latest). Never install older major versions.

```bash
cd apps/web/<app-name>

# React 19
pnpm add react@latest react-dom@latest

# Routing & data
pnpm add @tanstack/react-router @tanstack/react-query

# Forms & validation
pnpm add react-hook-form @hookform/resolvers valibot

# i18n
pnpm add react-i18next i18next

# Dev tooling — ESLint 10
pnpm add -D eslint@latest @eslint/js@latest typescript-eslint@latest globals
pnpm add -D @types/react@latest @types/react-dom@latest
pnpm add -D @storybook/react-vite storybook
pnpm add -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

### Step 3 — Configure TypeScript strict

Ensure `tsconfig.json` has:

```json
{
  "compilerOptions": {
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true
  }
}
```

### Step 4 — Configure ESLint flat config

Create `eslint.config.js`:

```javascript
import js from '@eslint/js';
import tseslint from 'typescript-eslint';
import globals from 'globals';

export default tseslint.config(js.configs.recommended, ...tseslint.configs.strictTypeChecked, {
  languageOptions: {
    globals: globals.browser,
    parserOptions: {
      projectService: true,
      tsconfigRootDir: import.meta.dirname,
    },
  },
  rules: {
    '@typescript-eslint/no-explicit-any': 'error',
    '@typescript-eslint/no-unused-vars': 'error',
    '@typescript-eslint/explicit-function-return-type': 'warn',
    '@typescript-eslint/consistent-type-imports': 'error',
  },
});
```

### Step 5 — Set up project structure

```
apps/web/<app-name>/
├── src/
│   ├── app/                    # App shell, providers, router
│   │   ├── providers/          # DI containers, context providers
│   │   ├── router/             # TanStack Router config
│   │   └── App.tsx
│   ├── features/               # Isolated feature modules
│   │   └── <feature>/
│   │       ├── atoms/
│   │       │   └── MyAtom/
│   │       │       ├── MyAtom.tsx
│   │       │       ├── MyAtom.module.css
│   │       │       └── index.ts
│   │       ├── molecules/
│   │       ├── organisms/
│   │       ├── templates/
│   │       ├── pages/
│   │       │   └── FeaturePage/
│   │       │       ├── FeaturePage.tsx
│   │       │       ├── FeaturePage.module.css
│   │       │       └── index.ts
│   │       ├── hooks/
│   │       ├── services/
│   │       ├── schemas/
│   │       ├── types/
│   │       ├── constants/
│   │       └── index.ts        # public API
│   ├── shared/                 # Cross-feature shared code
│   │   ├── atoms/              # Generic design system atoms
│   │   ├── molecules/          # Generic design system molecules
│   │   ├── hooks/
│   │   ├── services/
│   │   ├── types/
│   │   └── utils/
│   ├── tokens/                 # Design tokens (CSS custom properties)
│   ├── i18n/                   # i18n config and locale files
│   │   ├── locales/
│   │   │   ├── en.json
│   │   │   └── es.json
│   │   └── index.ts
│   └── main.tsx
├── .storybook/
├── eslint.config.js
├── tsconfig.json
├── rspack.config.ts
├── package.json
└── README.md
```

### Step 6 — Set up DI providers

Create a root provider that composes all dependency containers:

```typescript
// src/app/providers/AppProviders.tsx
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';
import { ApiProvider } from './ApiProvider';
import { I18nProvider } from './I18nProvider';

interface AppProvidersProps {
  children: React.ReactNode;
  apiClient?: ApiClient;       // injectable for testing
  queryClient?: QueryClient;   // injectable for testing
}

export function AppProviders({ children, apiClient, queryClient }: AppProvidersProps) {
  const client = queryClient ?? new QueryClient();

  return (
    <QueryClientProvider client={client}>
      <ApiProvider client={apiClient}>
        <I18nProvider>
          {children}
        </I18nProvider>
      </ApiProvider>
    </QueryClientProvider>
  );
}
```

### Step 7 — Follow project-architecture skill

Run the monorepo structure audit as defined in `.cursor/skills/project-architecture/SKILL.md`.
Update all READMEs (app, parent, root).

## Feature Development

When adding a feature, follow the `web-workflow` skill at `.cursor/skills/web-workflow/SKILL.md`.

## Styling — CSS Modules

Every component that needs styles has a colocated `.module.css` file:

```
Button/
├── Button.tsx
├── Button.module.css
└── index.ts
```

Rules:

- **NEVER use inline styles** (`style={{ }}`). Always `className`.
- Import as `import styles from './Button.module.css';`.
- Use CSS custom properties (`var(--ds-*)`) inside `.module.css` for token values.
- Styles are scoped to the component. No cross-component `.module.css` imports.
- Reusable values go in tokens or `styles/common.css`.

## Key Rules Reference

All web app code must follow these rules:

- `react-web-standards.mdc` — coding standards (includes CSS Modules mandate)
- `design-system-standards.mdc` — design system (no inline styles, tokens only)
- `web-quality-gate.mdc` — quality checks (inline style scan)
- `web-roles.mdc` — role workflow

---
> Source: [imenesesl/guidy](https://github.com/imenesesl/guidy) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-19 -->
