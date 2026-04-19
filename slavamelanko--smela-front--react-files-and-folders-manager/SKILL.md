---
name: react-files-and-folders-manager
description: | Convention    | Rule                                                           | Use when this capability is needed.
metadata:
  author: slavamelanko
---

# React Files and Folders Manager

## Core Conventions

| Convention    | Rule                                                           |
| ------------- | -------------------------------------------------------------- |
| Folder naming | `lowercase` = grouping folder, `PascalCase` = component folder |
| File naming   | `ComponentName.jsx` (not just `index.jsx`)                     |
| Exports       | Named exports with `export const` (except `ui/`)               |
| Barrel files  | `index.js` re-exports public components only                   |
| Functions     | Arrow functions (except `ui/`)                                 |

## Flat Structure

Keep folders flat.

- **Single file** → flat in grouping folder: `prompts/LoginPrompt.jsx`
- **2+ related files** → folder-per-component: `DataTable/` with hooks
- **Tests** → `__tests__/` in component or grouping folder
- **Stories** → `ComponentName.stories.jsx` or `foldername.stories.jsx`

## Building Blocks Pattern

When a grouping folder needs shared primitives, create `<FolderName>.jsx`:

| Folder      | File           | Contains                                       |
| ----------- | -------------- | ---------------------------------------------- |
| `form/`     | `Form.jsx`     | FormRoot, FormFields, FormLabel, FormError     |
| `settings/` | `Settings.jsx` | SettingsSection, SettingsLabel, SettingsOption |
| `pricing/`  | `Pricing.jsx`  | Bandwidth, Feature, PricePerUnit, TotalPrice   |

Order components top-to-bottom: containers → primitives.

```jsx
// form/Form.jsx
export const FormRoot = ({ children, className, ...props }) => (
  <form className={cn('flex flex-col gap-8', className)} {...props}>
    {children}
  </form>
)

export const FormLabel = ({ htmlFor, children, optional }) => (
  <label htmlFor={htmlFor}>
    {children}
    {!optional && <span className='ml-1 text-destructive'>*</span>}
  </label>
)
```

## Directory Structure

```txt
src/
├── components/
│   ├── Header/              # PascalCase = standalone component
│   │   ├── index.js
│   │   ├── Header.jsx
│   │   └── ProfileDropdown.jsx
│   ├── settings/            # lowercase = grouping folder
│   │   ├── index.js
│   │   ├── Settings.jsx     # Building blocks
│   │   └── DateTimeSettings/
│   ├── LanguageDropdown/
│   │   ├── LanguageDropdown.jsx
│   │   └── flags/           # Domain-specific assets
│   └── ui/                  # shadcn/ui ONLY
├── layouts/
├── pages/
│   ├── errors/
│   │   ├── General/
│   │   ├── NotFound/
│   │   └── Error.jsx        # Building blocks for sibling pages
│   └── admin/Users/
└── hooks/
```

## src/components/ui/ Rules

- Reserved for shadcn/ui primitives only
- Install via `npx shadcn@latest add <component>`
- Uses regular functions + default exports (shadcn convention)
- Custom wrappers go in `src/components/`, not `ui/`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/slavamelanko) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
