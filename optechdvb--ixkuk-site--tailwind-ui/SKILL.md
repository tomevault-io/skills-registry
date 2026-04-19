---
name: tailwind-ui
description: Use when creating or modifying UI components, working with Tailwind CSS styles, or implementing user interface features. This skill ensures consistency with Tailwind UI patterns and the project's design system.
metadata:
  author: optechdvb
---

# Tailwind UI Component Skill

You are working on a project with a **Tailwind UI license**. This skill guides you when working with UI components to ensure consistency, accessibility, and adherence to Tailwind UI patterns.

## Project UI Stack

- **Tailwind CSS**: 4.1.17
- **Headless UI**: 2.2.9 (unstyled, accessible components)
- **Heroicons**: 2.2.0 (SVG icons)
- **next-intl**: 4.5.8 (internationalization)

## Core Principles

### 1. Check Existing Components First
Before creating any new component:
- Search `src/ui/` for similar patterns
- Review `.claude/tailwind-ui-patterns/PATTERNS.md` for documented patterns
- Check example files in `.claude/tailwind-ui-patterns/` for reference

Existing components to reference:
- **Navigation**: `src/ui/Navbar.tsx` (Disclosure, Menu patterns)
- **Modals**: `src/ui/Dialog.tsx`, `src/ui/ContactModal.tsx` (Dialog pattern)
- **Forms**: `src/ui/ContactModal.tsx`, `src/ui/Subscribe.tsx` (form validation)
- **Footer**: `src/ui/Footer.tsx` (multi-column layout, social links)

### 2. Use Headless UI for Interactivity
For interactive components, always use Headless UI:
- **Menu/Dropdown**: `Menu`, `MenuButton`, `MenuItem`, `MenuItems`
- **Mobile Nav**: `Disclosure`, `DisclosureButton`, `DisclosurePanel`
- **Modals**: `Dialog`, `DialogPanel`, `DialogTitle`
- **Popovers**: `Popover`, `PopoverButton`, `PopoverPanel`
- **Other**: `Combobox`, `Listbox`, `RadioGroup`, `Switch`, `Tab`, `Transition`

Import from `@headlessui/react`

### 3. Always Add Internationalization
All user-facing text must be internationalized:

```tsx
import { useTranslations } from 'next-intl';

export default function MyComponent() {
  const t = useTranslations('namespace');

  return <button>{t('buttonLabel')}</button>;
}
```

**Remember**: Add translation keys to ALL locale files:
- `messages/en-us.ts`
- `messages/es-gt.ts`
- `messages/kek-gt.ts` (if applicable)
- `messages/quc-gt.ts` (if applicable)

### 4. Maintain Accessibility
Never compromise on accessibility:
- Use semantic HTML (`<nav>`, `<header>`, `<button>`, etc.)
- Include ARIA labels: `aria-label`, `aria-hidden`, `aria-describedby`
- Add screen reader text: `<span className="sr-only">Text</span>`
- Support keyboard navigation
- Include focus states: `focus:outline-*`, `focus-visible:outline-*`
- Maintain color contrast ratios (AA standard minimum)

### 5. Follow Mobile-First Responsive Design
Use Tailwind's responsive prefixes in mobile-first order:
```tsx
className="text-sm sm:text-base md:text-lg lg:text-xl"
```

Common breakpoints:
- Base: Mobile (< 640px)
- `sm:` Small tablets (≥ 640px)
- `md:` Tablets (≥ 768px)
- `lg:` Small desktops (≥ 1024px)
- `xl:` Large desktops (≥ 1280px)

### 6. Use Project Color Scheme
Reference colors from `tailwind.config.ts`:

**Brand Colors** (custom):
- `brand` - Deep blue (#0d47a1)
- `brand-light` - Light blue (#5472d3)
- `brand-dark` - Dark blue (#002171)
- `brand-gold` - Gold (#f9a825)

**Standard Colors**:
- Primary: `indigo-600`, `indigo-700`
- Neutral: `gray-*` (50, 100, 200, 300, 500, 600, 700, 800, 900)
- Text: `gray-900` (dark), `gray-600` (medium), `gray-500` (light)
- Backgrounds: `white`, `gray-50`, `gray-100`

**Dark Mode** (when applicable):
- Use `dark:` prefix: `dark:bg-gray-800`, `dark:text-white`

### 7. Add "use client" When Needed
If the component uses:
- React hooks (`useState`, `useEffect`, etc.)
- Event handlers (`onClick`, `onChange`, etc.)
- Browser APIs (`window`, `document`, etc.)

Add at the top of the file:
```tsx
"use client";
```

## Workflow for New Components

### Step 1: Determine if Pattern Exists
Check if a similar component exists in `src/ui/` or `.claude/tailwind-ui-patterns/`

### Step 2: Ask for Tailwind UI Example (if needed)
If creating a new pattern type not in the codebase:
- Ask the user: "Do you have a specific Tailwind UI example you'd like me to use?"
- Wait for them to provide the example code
- Proceed with adaptation

### Step 3: Adapt to Project Standards
When adapting Tailwind UI examples:

1. **Add i18n**:
   - Replace hardcoded strings with `t('key')`
   - Create translation keys in message files

2. **Use brand colors**:
   - Replace generic colors with project colors
   - Match existing component color schemes

3. **Add TypeScript types**:
   - Define prop interfaces
   - Type all function parameters and returns

4. **Add "use client" if needed**:
   - Check if component uses client-side features

5. **Follow file structure**:
   - Place in `src/ui/`
   - Use PascalCase filename matching component name
   - Export as default or named export

6. **Preserve accessibility**:
   - Keep all ARIA labels
   - Maintain semantic HTML
   - Don't remove sr-only text

### Step 4: Verify Integration
Ensure the component:
- Imports correctly with `@/ui/ComponentName`
- Has all required translations defined
- Matches responsive behavior of similar components
- Maintains accessibility standards

## Common Patterns

### Modal Dialog Pattern
```tsx
"use client";

import { Dialog, DialogPanel, DialogTitle } from '@headlessui/react';
import { useTranslations } from 'next-intl';

interface Props {
  open: boolean;
  onClose: () => void;
}

export default function MyModal({ open, onClose }: Props) {
  const t = useTranslations('myModal');

  return (
    <Dialog open={open} onClose={onClose} className="relative z-10">
      <div className="fixed inset-0 bg-gray-500/75" />
      <div className="fixed inset-0 z-10 overflow-y-auto">
        <div className="flex min-h-full items-center justify-center p-4">
          <DialogPanel className="w-full max-w-md bg-white rounded-lg p-6">
            <DialogTitle className="text-lg font-semibold">
              {t('title')}
            </DialogTitle>
            {/* Content */}
          </DialogPanel>
        </div>
      </div>
    </Dialog>
  );
}
```

### Dropdown Menu Pattern
```tsx
import { Menu, MenuButton, MenuItem, MenuItems } from '@headlessui/react';

<Menu as="div" className="relative">
  <MenuButton className="...">
    {/* Button content */}
  </MenuButton>
  <MenuItems className="absolute right-0 mt-2 w-48 bg-white rounded-md shadow-lg">
    <MenuItem>
      <a href="#" className="block px-4 py-2 text-sm">
        {/* Menu item */}
      </a>
    </MenuItem>
  </MenuItems>
</Menu>
```

### Mobile Navigation Pattern
```tsx
import { Disclosure, DisclosureButton, DisclosurePanel } from '@headlessui/react';
import { Bars3Icon, XMarkIcon } from '@heroicons/react/24/outline';

<Disclosure as="nav">
  <div className="flex h-16 justify-between">
    <DisclosureButton className="sm:hidden">
      <Bars3Icon className="block h-6 w-6 group-data-open:hidden" />
      <XMarkIcon className="hidden h-6 w-6 group-data-open:block" />
    </DisclosureButton>
  </div>
  <DisclosurePanel className="sm:hidden">
    {/* Mobile menu content */}
  </DisclosurePanel>
</Disclosure>
```

## When to Ask the User

Ask the user if:
1. Creating a new component type not in the codebase
2. Unsure which Tailwind UI pattern to follow
3. Need clarification on design requirements
4. Considering breaking from established patterns

## Quality Checklist

Before completing any UI component work, verify:
- [ ] Checked existing components for similar patterns
- [ ] Used Headless UI for interactive elements
- [ ] Added i18n with `useTranslations()`
- [ ] Updated all locale message files
- [ ] Used project brand colors
- [ ] Followed mobile-first responsive design
- [ ] Maintained accessibility (ARIA, semantic HTML, sr-only)
- [ ] Added "use client" if needed
- [ ] TypeScript types defined
- [ ] Component placed in `src/ui/`
- [ ] Tested keyboard navigation (mentally verified)

## Resources

- **Patterns docs**: `.claude/tailwind-ui-patterns/PATTERNS.md`
- **Example components**: `.claude/tailwind-ui-patterns/` (when user adds them)
- **Existing components**: `src/ui/`
- **Brand colors**: `tailwind.config.ts`
- **Translations**: `messages/{locale}.ts`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/optechdvb) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
