---
name: react-ui
description: > Use when this capability is needed.
metadata:
  author: acampb
---

# React UI

Create and maintain React components with shadcn/ui, React Hook Form + Zod forms, theme variables, and shared component patterns.

## Sub-Commands

| Command            | Purpose                                   | Modifies Code? |
| ------------------ | ----------------------------------------- | -------------- |
| `/react-ui create`       | Create new reusable components            | Yes (approval) |
| `/react-ui extract`      | Move types/components to shared packages  | Yes (approval) |
| `/react-ui componentize` | Break down large components (SRP)         | Yes (approval) |
| `/react-ui patterns`     | Apply project conventions (sheets, forms) | Yes (approval) |
| `/react-ui modernize`    | Upgrade to latest shadcn UX patterns      | Yes (approval) |
| `/react-ui review`       | Quality audit - report issues, no changes | No             |

**All sub-commands ask before making changes.**

---

## Before Any Sub-Command

1. **Read** [references/subcommands.md](references/subcommands.md) - Detailed workflow for the sub-command you need
2. **Scan** the project's component library for existing patterns — `packages/ui/src/components/` in monorepos, `components/` in standalone projects (if exists)
3. **Scan** the project's theme variables — `packages/ui/src/globals.css` in monorepos, `src/globals.css` or `app/globals.css` in standalone (if exists)

---

## Critical Rules (All Sub-Commands)

### 1. Theme Variables Only

**Never use raw Tailwind colors.** Always use theme variables from `globals.css`:

```typescript
// NEVER - raw Tailwind colors
className = 'text-green-500 bg-green-50 border-green-500/50'

// ALWAYS - theme variables
className = 'text-success bg-success-muted border-success/20'
```

**Common theme colors (define in your `globals.css`):**

- `primary`, `secondary`, `muted`, `accent`
- `success`, `warning`, `destructive`, `info` (each with `-foreground` and `-muted` variants)
- `border`, `input`, `ring`, `background`, `foreground`
- `card`, `popover` (each with `-foreground`)

**Color format:** shadcn/ui uses OKLCH (newer projects) or HSL (older projects). Either works — the critical rule is semantic variable names, not the underlying format. Tailwind v4 projects use `@theme` in CSS instead of `tailwind.config.js`.

**No globals.css yet?** **Read** [references/patterns-guide.md#globalscss-template](references/patterns-guide.md#globalscss-template) for a complete Tailwind v4 template with OKLCH variables.

### 2. Use Existing UI Patterns First

Before creating anything new, check existing components (`packages/ui/src/components/` in monorepos, `components/` in standalone). Common pattern types:

**Sheets:** Action sheets (form submissions with footer buttons) vs Settings sheets (configuration with inline actions).

**Forms:** Complete field components (TextField, SelectField, etc.) with label, description, and validation built in.

**Layout:** Section + SectionHeader for grouping, PageHeader for page titles, KPICard for metrics, EmptyState for placeholders, DangerZone for destructive actions.

**Read** [references/patterns-guide.md](references/patterns-guide.md) for pattern recipes.

### 3. Complete Fields in App Code

Build **complete field components** in your shared UI package (composing shadcn Field primitives internally). App code consumes the complete fields — never assembles primitives:

```typescript
// CORRECT - Complete field (app code)
<PercentField label="Discount" description="Enter 0-100%" value={value} onChange={setValue} required />

// WRONG - Assembling primitives at page level
<Field>
  <FieldLabel>Discount</FieldLabel>
  <PercentInput value={value} onChange={setValue} />
</Field>
```

shadcn provides composable `Field`, `FieldLabel`, `FieldDescription`, and `FieldError` primitives. Use these **inside** your shared library when building complete fields — don't expose them to app code.

### 4. React Hook Form + Zod for All Forms

**Never use `useState` per form field:**

```typescript
// ALWAYS - React Hook Form + Zod
const schema = z.object({
  name: z.string().min(1, 'Name is required'),
  email: z.string().email('Invalid email')
})

const form = useForm<z.infer<typeof schema>>({
  resolver: zodResolver(schema),
  defaultValues: { name: '', email: '' }
})

// NEVER - useState per field
const [name, setName] = useState('')
```

**Use shared schemas** from your contracts package when available. Local schemas only for purely UI validations.

**Watch values reactively** with `useWatch` when UI needs to react to form field changes.

**Zod v4 note:** If using Zod v4+, use `error` instead of `message` in refinements, `.extend()` instead of `.merge()`, and `z.file()` for file uploads.

### 5. Ask Before Coding

All sub-commands (except review) must: analyze code, present plan, wait for approval.

### 6. Icon Sizing in Buttons

Use `size-4` class. Button handles gap spacing, so `mr-2` is unnecessary:

```typescript
// CORRECT
<Button><PlusIcon className="size-4" />Create</Button>

// WRONG
<Button><PlusIcon className="mr-2 h-4 w-4" />Create</Button>
```

### 7. App Shell Provides Layout Spacing

Content components should use fragments, not wrapper divs - the shell provides `space-y-6`.

### 8. Theme Variable Parity Across Apps

When sharing components across multiple apps, verify all consuming apps define the required CSS variables.

### 9. PageHeader for Detail Pages

Detail pages need full treatment: icon + title + description + actions. Primary CTA uses default variant (solid), secondary uses `variant="outline"`.

### 10. Section Pattern for Content Groups

Use `Section` and `SectionHeader` for semantic grouping instead of raw divs with manual styling.

### 11. DataTable Column Memoization

When DataTable columns reference callbacks, you MUST wrap callbacks in `useCallback` and columns in `useMemo`. Without this, columns recreate every render causing performance issues.

**React Compiler:** If the project uses React Compiler, manual memoization is handled automatically and this rule can be relaxed.

**Table vs DataTable:** Use DataTable for data management (sorting, filtering, pagination). Use raw Table for static summary displays (max ~5 items).

### 12. Accessibility Defaults

All form fields must connect errors to inputs via `aria-invalid` and `aria-describedby`. On submit failure, call `form.setFocus()` on the first errored field. Dynamic error messages need `aria-live="polite"`. Custom interactive components must support keyboard navigation. Theme variable colors must meet WCAG contrast requirements.

### 13. shadcn CLI for All Primitives

**Never recreate shadcn components manually.** Always install via CLI:

```bash
# Monorepo — install to shared UI package
pnpm dlx shadcn@latest add {component} --cwd packages/ui

# Standalone project
pnpm dlx shadcn@latest add {component}
```

- **Always `pnpm dlx`**, never `npx` — pnpm monorepos require `pnpm dlx` for correct resolution
- **Always `--cwd packages/ui`** in monorepos — primitives belong in the shared package
- After adding, promote new Radix dependencies to the pnpm catalog (`pnpm-workspace.yaml`)
- For monorepo initialization (`components.json`, directory setup), **Read** the js-monorepo skill's [shadcn-setup.md](../js-monorepo/references/shadcn-setup.md)

---

## Component Design Principles

**Read** [references/component-design.md](references/component-design.md) for full guidance on:

- Compound components over render props
- Variant props over raw classes
- Icon sizing internal to components
- Semantic components over abstract primitives
- Component extension principles
- Config derivation patterns
- React Server Component classification (`'use client'` rules)
- Container queries for shared components

---

## `/react-ui review`

Quality audit without code changes. Lists issues and suggests which sub-commands to run.

### When to Use

- Before committing UI changes
- Periodic quality check on a feature area
- After importing or inheriting components from another project

### Review Checklist

```
THEME COMPLIANCE
  [ ] No raw Tailwind colors
  [ ] Uses theme variables
  [ ] Dark mode compatible

PATTERN USAGE
  [ ] Sheets use project patterns
  [ ] Forms use React Hook Form + Zod
  [ ] Errors use proper components
  [ ] Fields use complete field components

SOLID PRINCIPLES
  [ ] Single Responsibility per component
  [ ] Components < 150 lines render
  [ ] Focused props interface

EXTRACTION CANDIDATES
  [ ] Types for shared contracts
  [ ] Components reusable across apps
  [ ] Schemas that could be shared

ACCESSIBILITY
  [ ] Form errors use aria-invalid + aria-describedby
  [ ] Submit failure focuses first error field
  [ ] Dynamic messages use aria-live="polite"
  [ ] Custom components support keyboard navigation

REACT BEST PRACTICES
  [ ] No useEffect for data fetching
  [ ] Proper key props on lists
  [ ] Memoization where appropriate
  [ ] Skeleton returned instead of null
```

### Must Flag (Critical Violations)

| Pattern Found                                  | Should Be                     | Severity |
| ---------------------------------------------- | ----------------------------- | -------- |
| Assembling field primitives manually            | Complete field component       | Critical |
| Raw error alert markup                          | Form error component           | Critical |
| Mutation `onSuccess` without toast              | Add toast notification         | Critical |
| Mutation `onError` without inline error display | form.setError or setState      | Critical |
| Raw Tailwind color + opacity                    | Theme variable                 | Critical |
| Raw div danger zone markup                      | DangerZone component           | Warning  |
| DataTable columns with callbacks, no useMemo    | Wrap in useMemo                | Warning  |
| Form field missing aria-invalid/aria-describedby | Connect error state to input  | Warning  |
| Missing 'use client' on component using hooks   | Add directive                  | Critical |
| Unnecessary fragment `<><Single /></>`          | Return element directly         | Info     |

---

## Checklist

Before completing any sub-command:

- [ ] No raw Tailwind colors remain
- [ ] All sheets use project sheet patterns
- [ ] All forms use React Hook Form + Zod
- [ ] Types are in shared contracts if shared
- [ ] Components are in shared UI package if reusable
- [ ] Skeletons exist for new shared components (if applicable)
- [ ] Exports added to package index.ts files (if exists)
- [ ] Imports updated in consuming files

---

## Related Skills

- **`/nextjs-data`** - SSR hydration, React Query, data fetching patterns. Use for page creation, query layers, and server/client data flow.
- **`/js-monorepo`** - Monorepo infrastructure (pnpm, Turborepo, shared packages). Use when setting up the repo or adding shared packages.

---

## Reference Files

- Sub-command workflows: [references/subcommands.md](references/subcommands.md)
- Component design principles: [references/component-design.md](references/component-design.md)
- Pattern recipes: [references/patterns-guide.md](references/patterns-guide.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/acampb) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
