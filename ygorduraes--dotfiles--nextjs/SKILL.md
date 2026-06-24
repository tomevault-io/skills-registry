---
name: next-js
description: Define best practices when working with Next.js projects Use when this capability is needed.
metadata:
  author: ygorduraes
---

## When to use me

Use this every time you're working on Next.js projects

## General

- Always use `pnpm` if you need to run commands
- Don't overengineer. Be as simple as possible (KISS), refactor most of the code to simplify it if necessary
- Always run `pnpm lint` after making changes to see if everything is fine, and `pnpm format` to format the codebase with the code formatter
- Never make changes on other repos other than the one you're working on unless specifically told to

## UI Components & Styling

- Use shadcn/ui for every component possible (cards, accordion, sidebar, breadcrumbs, badges, alerts, React Hook Form, charts etc)
- Avoid creating components from scratch at all costs. Always check the available components in shadcn's MCP registry before trying to create components
- The command to add new components is like this `pnpm dlx shadcn@latest add button`. Check shadcn's MCP registry to find out the name of the component
- Use Lucide for icons
- Prefer using shadcn/ui Skeleton component when loading data instead of using texts or spinners

## Component Architecture

- Break the code in separate components every time you have to reuse stuff, avoid creating giant files
- When creating new pages, always keep a server side `page.tsx` along with its client side component e.g. for a `/login` page, there would be a `use server` `page.tsx` that loads a `use client` `login.tsx`, both files inside `/login`. This way we can load server side data before loading the page when necessary.
- Never export default on components and functions, always export the functions individually
- Always protect props with Readonly

## Data Fetching & APIs

- When mocking data, just mock it straight into the components (don't create APIs or server functions to fetch data, I'll do that later when I get the actual API schema). Use simple states (useState/useEffect) for the mock data. No need for useMemos, SWR or more complex stuff.
- When fetching real data, do it in the UI through useSWR, always creating a custom fetcher in `lib/fetchers.ts` and a server function for it in `lib/api-functions.ts`
- If you've got an external API to connect to, create interfaces/types that match the fields returned, even if it uses snake case
- Always use async params fetching on slugs (mandatory for newer versions of Next.js)

## Code Quality & Conventions

- Always document the functions using @param and @returns
- Prefer async/await over .then() all the time
- Prefer `??` over `||` to prevent nulls (adapt the code accordingly)
- Avoid ternaries unless when conditionally rendering TSX code that contains Javascript/Typescript
- Keep cognitive complexity below 15 for functions and other blocks

## Testing

- If we're working with tests, always write/update the test files after changes, minimum coverage for new code is 80%

## Code Style Guidelines

### TypeScript

**Strict Mode**: Enabled in `tsconfig.json` - all code must satisfy strict type checking.

**Type Definitions**:

- Always provide explicit return types for functions
- Use TypeScript's utility types (Omit, Pick, Partial, etc.)
- Define interfaces for component props
- Use `type` for unions, `interface` for object shapes
- Maintain type safety - no `any` types unless absolutely necessary

**Imports**: Use `@/` alias for absolute imports (configured in `tsconfig.json`):

### React & Next.js

**Component Structure**:

- This is a Next.js App Router project (not Pages Router)
- Prefer server-side data fetching over client-side
- Use function declarations (not arrow functions) for named exports
- Server components are default (no directive needed)
- Use `async` for server components that fetch data

**Props**:

- Use `React.ComponentProps<T>` to extend built-in element props
- Destructure props with default values in function signature

### Naming Conventions

**Files**:

- Components: `kebab-case.tsx` (e.g., `theme-toggle.tsx`)
- Pages: `page.tsx`, `layout.tsx`, `loading.tsx`, `error.tsx`
- Utils: `kebab-case.ts` (e.g., `supabase-client.ts`)

**Variables & Functions**:

- camelCase for variables and functions
- PascalCase for components and types
- SCREAMING_SNAKE_CASE for constants

### Environment Variables

- Store in `.env.local` if needed (gitignored)
- Access via `process.env.VARIABLE_NAME` in a declared const

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ygorduraes) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
