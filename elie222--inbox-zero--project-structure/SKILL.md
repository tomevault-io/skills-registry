---
name: project-structure
description: Project structure and file organization guidelines Use when this capability is needed.
metadata:
  author: elie222
---
# Project Structure

## Main Structure

- We use Turborepo with pnpm workspaces
- Main app is in `apps/web`
- Packages are in the `packages` folder
- Server actions are in `apps/web/utils/actions` folder

```tree
.
├── apps
│   ├── web/             # Main Next.js application
│   │   ├── app/         # Next.js App Router
│   │   │   ├── (app)/   # Main application pages
│   │   │   │   ├── assistant/     # AI assistant feature
│   │   │   │   ├── reply-zero/     # Reply Zero feature
│   │   │   │   ├── settings/       # User settings
│   │   │   │   ├── setup/          # Main onboarding
│   │   │   │   ├── clean/          # Bulk email cleanup
│   │   │   │   ├── smart-categories/ # Smart sender categorization
│   │   │   │   ├── bulk-unsubscribe/ # Bulk unsubscribe
│   │   │   │   ├── stats/          # Email analytics
│   │   │   │   ├── mail/           # Email client (in beta)
│   │   │   │   └── ... (other app routes)
│   │   │   ├── api/    # API Routes
│   │   │   │   ├── knowledge/      # Knowledge base API
│   │   │   │   ├── reply-tracker/  # Reply tracking
│   │   │   │   ├── clean/          # Cleanup API
│   │   │   │   ├── ai/            # AI features API
│   │   │   │   ├── user/          # User management
│   │   │   │   ├── google/        # Google integration
│   │   │   │   ├── auth/          # Authentication
│   │   │   │   └── ... (other APIs)
│   │   │   ├── (landing)/  # Marketing/landing pages
│   │   │   ├── blog/       # Blog pages
│   │   │   ├── layout.tsx  # Root layout
│   │   │   └── ... (other app files)
│   │   ├── utils/       # Utility functions and helpers
│   │   │   ├── actions/    # Server actions
│   │   │   ├── ai/         # AI-related utilities
│   │   │   ├── llms/       # Language model utilities
│   │   │   ├── gmail/      # Gmail integration utilities
│   │   │   ├── redis/      # Redis utilities
│   │   │   ├── user/       # User-related utilities
│   │   │   ├── parse/      # Parsing utilities
│   │   │   ├── queue/      # Queue management
│   │   │   ├── error-messages/  # Error handling
│   │   │   └── *.ts        # Other utility files (auth, email, etc.)
│   │   ├── public/      # Static assets (images, fonts)
│   │   ├── prisma/      # Prisma schema and client
│   │   ├── styles/      # Global CSS styles
│   │   ├── providers/   # React Context providers
│   │   ├── hooks/       # Custom React hooks
│   │   ├── sanity/      # Sanity CMS integration
│   │   ├── __tests__/   # AI test files (Vitest)
│   │   ├── scripts/     # Utility scripts
│   │   ├── store/       # State management
│   │   ├── types/       # TypeScript type definitions
│   │   ├── next.config.mjs
│   │   ├── package.json
│   │   └── ... (config files)
├── packages
    ├── tinybird/
    ├── loops/
    ├── resend/
    ├── tinybird-ai-analytics/
    └── tsconfig/
```

## File Naming and Organization

- Use kebab case for route directories (e.g., `api/hello-world/route`)
- Use PascalCase for components (e.g. `components/Button.tsx`)
- Shadcn components are in `components/ui`
- All other components are in `components/`
- Colocate files in the folder where they're used unless they can be used across the app
- If a component can be used in many places, place it in the `components` folder

## New Pages

- Create new pages at: `apps/web/app/(app)/PAGE_NAME/page.tsx`
- Components for the page are either in `page.tsx` or in the `apps/web/app/(app)/PAGE_NAME` folder
- Pages are Server components for direct data loading
- Use `swr` for data fetching in deeply nested components
- Components with `onClick` must be client components with `use client` directive
- Server action files must start with `use server`

## Utility Functions

- Create utility functions in `utils/` folder for reusable logic
- Use lodash utilities for common operations (arrays, objects, strings)
- Import specific lodash functions to minimize bundle size:
  ```ts
  import groupBy from "lodash/groupBy";
  ```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/elie222) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
