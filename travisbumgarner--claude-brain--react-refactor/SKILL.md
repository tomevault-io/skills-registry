---
name: react-refactor
description: Refactor large React components into a well-organized project structure. Use when the user has oversized React files (typically 300+ lines) and wants them broken into meaningful abstractions following a standard directory layout with shared components, page-level components, and shared frontend/backend types. Prefers Material UI, avoids over-splitting small components, and aims for a healthy balance of medium-sized files. Use when this capability is needed.
metadata:
  author: travisbumgarner
---

# React Refactor Skill

Break down large React components into a well-structured, maintainable codebase. This skill applies opinionated conventions around directory structure, component granularity, and shared code organization.

---

## When to Use

- A single React file is **300+ lines** and contains multiple logical concerns.
- A component mixes page layout, data fetching, business logic, reusable UI, and types in one file.
- The user says things like "clean up", "refactor", "split this component", "organize my React code".
- The user has a monolithic `App.tsx` or a giant page component.

## When NOT to Refactor

- A component is **under ~150 lines** and handles a single concern — leave it alone.
- Splitting would create files under ~40 lines that are just pass-through wrappers.
- The "component" is really just a styled element or a one-liner — keep it inline.

**The goal is medium-sized files (roughly 80–250 lines each), not a proliferation of tiny ones.**

---

## Directory Structure Convention

All refactors must target this directory layout:

```
project-root/
├── shared/
│   └── src/
│       ├── types/              # Types shared between frontend AND backend
│       │   ├── api.ts          # Request/response shapes
│       │   ├── models.ts       # Domain models, entities
│       │   └── index.ts        # Barrel export
│       ├── constants.ts        # Shared constants, enums
│       └── utils.ts            # Pure utility functions used by both sides
│
├── frontend/
│   └── src/
│       ├── App.tsx
│       │
│       ├── components/         # Components used directly by App.tsx
│       │   ├── AppHeader.tsx
│       │   ├── AppSidebar.tsx
│       │   └── AppLayout.tsx
│       │
│       ├── sharedComponents/   # Components reused across multiple pages/features
│       │   ├── DataTable.tsx
│       │   ├── ConfirmDialog.tsx
│       │   ├── LoadingOverlay.tsx
│       │   └── StatusChip.tsx
│       │
│       ├── hooks/              # Shared custom hooks
│       │   └── useAuth.ts
│       │
│       └── pages/
│           ├── Dashboard/
│           │   ├── Dashboard.tsx
│           │   ├── index.ts             # Re-exports Dashboard.tsx
│           │   └── components/          # Components ONLY used by Dashboard
│           │       ├── MetricsCard.tsx
│           │       └── ActivityFeed.tsx
│           │
│           └── Settings/
│               ├── Settings.tsx
│               ├── index.ts
│               └── components/
│                   └── SettingsForm.tsx
```

### Placement Rules

| What it is | Where it goes |
|---|---|
| Types/interfaces used by both frontend and backend (API contracts, domain models, shared enums) | `shared/src/types/` |
| Pure utility functions or constants used by both frontend and backend | `shared/src/` |
| A component used by **2+ pages or features** | `frontend/src/sharedComponents/` |
| A component used **only by App.tsx** (layout, nav, top-level wrappers) | `frontend/src/components/` |
| A page-level component (a route target) | `frontend/src/pages/[PageName]/[PageName].tsx` with an `index.ts` |
| A component used **only within one page** | `frontend/src/pages/[PageName]/components/` |
| Custom hooks shared across pages | `frontend/src/hooks/` |
| Custom hooks used only by one page | Co-locate in the page directory |

### Page Index Pattern

Every page directory **must** have an `index.ts` that re-exports the page component:

```typescript
// frontend/src/pages/Dashboard/index.ts
export { default } from './Dashboard';
```

This keeps imports clean: `import Dashboard from '@/pages/Dashboard'`.

---

## Refactoring Process

### Step 1: Analyze the Component

Before writing any code, read **all** target files and identify:

1. **Distinct UI sections** — Header, sidebar, main content, modals, forms, etc.
2. **Data fetching / state management** — API calls, complex `useState`/`useReducer` blocks, context usage.
3. **Reusable patterns** — Are there repeated UI patterns (tables, cards, dialogs) that appear in other files too?
4. **Types and interfaces** — Which are API contracts (shared) vs. local component props (stay local)?
5. **Business logic** — Validation, data transformation, computed values — candidates for hooks or utils.
6. **Cross-file duplication** — Compare all target files against each other. If two files contain structurally similar JSX blocks, handler patterns, or conditional rendering sections (even if not line-for-line identical), that is a **high-priority extraction target** into `sharedComponents/`. Cross-file duplication always trumps the line-count heuristics below — extract it regardless of size.

### Step 2: Plan the Split (Before Coding)

Outline the target files and what goes in each. Apply these judgment calls:

- **Extract a component** when a JSX block is 50+ lines with its own logic, OR when it represents a distinct UI concept (e.g., a form, a card, a modal).
- **Extract a custom hook** when stateful logic (useState + useEffect + handlers) is 30+ lines and has a clear name like `useDashboardFilters` or `useFormValidation`.
- **Extract types to `shared/`** when the same interface appears in both API route handlers and frontend components.
- **DON'T extract** a 20-line JSX fragment that's just some `<Box>` and `<Typography>` with no logic. Inline JSX with no independent state is fine to keep in the parent.
- **DON'T create** a component file for a single styled `<Button>` variant. Use `sx` props or a small helper in the same file.

**Ask yourself: would this extraction result in two medium files, or one medium file and one tiny file? If the latter, skip it.**

### Step 3: Execute the Refactor

1. **Start with types.** Move shared types to `shared/src/types/`. Keep component-local prop types in the component file.
2. **Extract shared components** to `frontend/src/sharedComponents/` — things like data tables, dialogs, status indicators that are used (or will be used) in multiple places.
3. **Extract page-level components** to `frontend/src/pages/[Page]/components/` — sections of a page that are large enough to warrant their own file.
4. **Extract hooks** when a logical block of state + effects + handlers can be cleanly named and isolated.
5. **Wire up imports** and ensure the parent component is now a clean orchestrator: it composes child components, passes props, and manages top-level page state.

### Step 4: Validate

- No file should be under ~40 lines unless it's a pure type file or an `index.ts` barrel.
- No file should be over ~300 lines. If it is, consider further splitting.
- Every extracted component should have a clear, single responsibility.
- Material UI components should be used throughout — no raw HTML `<div>`, `<button>`, `<input>` when MUI equivalents exist.
- Imports should be clean. Use barrel exports (`index.ts`) for directories when they have 3+ exports.

---

## Material UI Conventions

**Always prefer MUI components over raw HTML elements:**

| Instead of | Use |
|---|---|
| `<div>` (for layout) | `<Box>`, `<Stack>`, `<Grid>`, `<Container>` |
| `<button>` | `<Button>`, `<IconButton>`, `<LoadingButton>` |
| `<input>` | `<TextField>`, `<Select>`, `<Autocomplete>` |
| `<table>` | `<Table>` + components, or `<DataGrid>` |
| `<ul>/<li>` | `<List>`, `<ListItem>`, `<ListItemText>` |
| `<h1>`–`<h6>`, `<p>`, `<span>` | `<Typography variant="...">` |
| `<a>` | `<Link>` (MUI) or router `<Link>` |
| `<dialog>` / custom modals | `<Dialog>`, `<DialogTitle>`, `<DialogContent>`, `<DialogActions>` |
| Custom loading spinners | `<CircularProgress>`, `<LinearProgress>`, `<Skeleton>` |
| Custom tooltips | `<Tooltip>` |
| CSS media queries for layout | MUI `useMediaQuery`, `<Grid>`, or `sx` breakpoints |

**Styling approach:**
- Use the `sx` prop for one-off styles.
- Use `styled()` for components that are reused with consistent custom styling.
- Do NOT use external CSS files or CSS modules — keep styles co-located via `sx` or `styled`.

---

## Granularity Heuristics

These are guidelines, not hard rules. Use judgment.

| Signal | Action |
|---|---|
| Two+ files have structurally similar JSX/logic blocks | **Always** extract to `sharedComponents/` — duplication overrides size rules |
| File is 500+ lines | Almost certainly needs splitting |
| File is 300–500 lines | Likely needs splitting — look for 2-3 natural seams |
| File is 150–300 lines | Maybe split if there are clearly distinct concerns; otherwise leave it |
| File is under 150 lines | Leave it alone unless it's doing two completely unrelated things |
| A JSX block is 80+ lines with its own state | Extract to a component |
| A JSX block is 30 lines, no state | Probably leave inline |
| A hook body is 40+ lines | Extract to a custom hook file |
| A hook body is 15 lines | Keep it in the component |
| 3+ components would use the same UI pattern | Put it in `sharedComponents/` |
| 2 components use the same UI pattern | Put it in `sharedComponents/` — don't wait for a third consumer |
| Only 1 component uses a UI pattern | Keep it local to that page's `components/` |

---

## Common Refactoring Patterns

### Pattern: Page with Multiple Sections

**Before:** One 600-line page with header, filters, data table, and detail modal all inline.

**After:**
```
pages/Orders/
├── Orders.tsx           (~120 lines — orchestrates layout, manages selected order state)
├── index.ts
└── components/
    ├── OrderFilters.tsx  (~100 lines — filter state, MUI TextFields/Selects)
    ├── OrderTable.tsx    (~120 lines — columns config, row rendering, pagination)
    └── OrderDetail.tsx   (~80 lines — MUI Dialog showing order info)
```

### Pattern: Shared Data Table

If multiple pages have similar tables (sortable, filterable, paginated), extract once:

```
sharedComponents/
└── DataTable.tsx         (~200 lines — generic sortable/filterable table using MUI DataGrid)
```

Pages then use `<DataTable columns={...} rows={...} />` instead of reimplementing.

### Pattern: Form with Validation

**Before:** A 400-line component with 15 form fields, validation logic, and submission handling.

**After:**
```
pages/CreateUser/
├── CreateUser.tsx        (~80 lines — layout, submit handler, success/error states)
├── index.ts
└── components/
    └── UserForm.tsx      (~180 lines — form fields, validation, MUI TextFields)

shared/src/types/
└── user.ts               (CreateUserRequest, CreateUserResponse, UserFormData)
```

### Pattern: App Shell

**Before:** `App.tsx` is 500 lines with routing, auth checks, sidebar, header, theme, and error boundaries.

**After:**
```
App.tsx                   (~80 lines — ThemeProvider, Router, AuthProvider, <AppLayout>)
components/
├── AppLayout.tsx         (~60 lines — sidebar + header + main content area)
├── AppHeader.tsx         (~100 lines — logo, nav, user menu)
└── AppSidebar.tsx        (~120 lines — nav links, collapse logic)
```

---

## Checklist for the Final Output

Before delivering the refactored code:

- [ ] No raw HTML where MUI components exist.
- [ ] No file under 40 lines (except `index.ts` barrels and pure type files).
- [ ] No file over 300 lines.
- [ ] Shared API types live in `shared/src/types/`.
- [ ] Components used by 2+ pages are in `sharedComponents/`.
- [ ] Page-local components are in `pages/[Page]/components/`.
- [ ] Every page directory has an `index.ts`.
- [ ] `App.tsx`-level components are in `frontend/src/components/`.
- [ ] Imports are clean and relative paths are reasonable.
- [ ] The total number of new files is justified — no gratuitous splitting.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/travisbumgarner) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
