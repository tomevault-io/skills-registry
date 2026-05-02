---
name: carbon-icons
description: Use when installing, searching, or integrating IBM Carbon Icons. Prevents icon name hallucination by validating icons exist in @carbon/icons-react before use. Handles migrations from Lucide, Heroicons, FontAwesome, or other icon libraries to Carbon.
metadata:
  author: jameswlepage
---

# Carbon Icons

## When to use

Use this skill when:

- Installing or setting up `@carbon/icons-react` or `@carbon/icons`
- Searching for specific icons by name, concept, or function
- Migrating from another icon library (Lucide, Heroicons, FontAwesome, etc.) to Carbon
- Verifying an icon exists before using it
- Finding the correct icon name for a UI element

## Critical rule: Never hallucinate icon names

**ALWAYS verify icons exist before using them.** Carbon has 2,500+ icons with specific naming conventions. Do not guess icon names.

### Verification procedure

1. **After npm install**, search the actual installed package:
   ```bash
   node scripts/search-carbon-icons.mjs "<search-term>"
   ```

2. **Or search the node_modules directly**:
   ```bash
   ls node_modules/@carbon/icons-react/es/index.js | xargs grep -i "<search-term>" || \
   ls node_modules/@carbon/icons/es/index.js | xargs grep -i "<search-term>"
   ```

3. **Or use the categories reference** at `references/categories.md` to browse icons by category.

## Inputs required

- Project path and framework (React/Vue/Angular/vanilla)
- Target icon(s) or concept to search for
- If migrating: source library and icon names to replace

## Procedure

### 1) Install the package

For React projects (most common):
```bash
npm install @carbon/icons-react
```

For other frameworks or vanilla:
```bash
npm install @carbon/icons
```

### 2) Search for icons

Run the search script from the skill directory:
```bash
node skills/carbon-icons/scripts/search-carbon-icons.mjs "<search-term>"
```

Example searches:
- `search-carbon-icons.mjs "add"` → Add, AddAlt, AddFilled, AddLarge
- `search-carbon-icons.mjs "settings"` → Settings, SettingsAdjust, SettingsCheck, SettingsEdit, etc.
- `search-carbon-icons.mjs "user"` → User, UserAdmin, UserAvatar, UserFollow, etc.

### 3) Import and use the icon

Icons are exported as PascalCase React components:

```tsx
import { Add, Settings, User } from '@carbon/icons-react';

// Default size is 16px
<Add />

// Specify size: 16, 20, 24, or 32
<Settings size={24} />

// Custom styling
<User className="text-blue-500" />
```

### 4) Migration from other libraries

When replacing icons from another library:

1. **Identify the current icon** and its semantic meaning
2. **Search Carbon for equivalent** using the search script
3. **Choose the best match** - Carbon often has multiple variants:
   - Base icon: `Settings`
   - Filled variant: `SettingsFilled`
   - Outline variant: `SettingsOutline`
   - Alt variant: `SettingsAlt`

Common mappings from Lucide → Carbon:
- `Plus` → `Add`
- `X` / `Close` → `Close`
- `Check` → `Checkmark`
- `ChevronRight` → `ChevronRight`
- `Search` → `Search`
- `Menu` → `Menu`
- `Home` → `Home`
- `Trash` → `TrashCan`
- `Edit` / `Pencil` → `Edit`
- `Copy` → `Copy`
- `Download` → `Download`
- `Upload` → `Upload`
- `Settings` / `Cog` → `Settings`
- `User` → `User`
- `Mail` → `Email`
- `Phone` → `Phone`
- `Calendar` → `Calendar`
- `Clock` → `Time`
- `Eye` → `View` / `ViewFilled`
- `EyeOff` → `ViewOff` / `ViewOffFilled`

**Always verify these exist** - the mappings above are starting points, not guarantees.

## Icon naming conventions

Carbon icons follow these patterns:

- **Base name**: `Settings`, `User`, `Document`
- **Filled variant**: `SettingsFilled`, `UserFilled`
- **Outline variant**: `CheckmarkOutline`
- **Alt variant**: `AddAlt`, `CloseAlt`
- **Size in name** (deprecated): Some old icons have size suffixes, prefer size prop instead
- **Compound names**: Use double-dash in source, PascalCase in import
  - `bottom-panel--close` → `BottomPanelClose`
  - `text--align--center` → `TextAlignCenter`

## Icon sizes

Carbon icons support 4 standard sizes via the `size` prop:

- `16` (default) - Inline text, compact UI
- `20` - Standard buttons, form elements
- `24` - Headers, prominent actions
- `32` - Hero sections, large displays

```tsx
<Add size={16} />  // 16x16px
<Add size={20} />  // 20x20px
<Add size={24} />  // 24x24px
<Add size={32} />  // 32x32px
```

## Styling icons

Icons accept standard SVG/React props:

```tsx
// Color via fill or className
<Add fill="#0f62fe" />
<Add className="text-blue-600" />

// Accessibility
<Add aria-label="Add item" />

// Two-tone icons (some icons support this)
<SomeIcon data-icon-path="inner-path" fill="#different-color" />
```

## Verification

After implementation:

- [ ] Icons render at correct size
- [ ] Icons have appropriate aria-labels for accessibility
- [ ] No console warnings about missing icons
- [ ] Icon names match what's actually imported (no typos)

## Failure modes / debugging

### "Icon not found" or import error
The icon name doesn't exist. Run the search script to find valid names.

### Icon renders as empty/broken
Check that you're importing from the correct package (`@carbon/icons-react` vs `@carbon/icons`).

### Size looks wrong
Verify you're using one of the standard sizes (16, 20, 24, 32). Custom sizes may not render correctly.

### Icon color not applying
Carbon icons use `fill` for color. If using Tailwind, use `fill-current` with text color classes.

## Escalation

- [Carbon Icons Library](https://www.ibm.com/design/language/iconography/ui-icons/library/) - Visual browser
- [Carbon Icons React docs](https://carbondesignsystem.com/elements/icons/code/)
- [GitHub: carbon-design-system/carbon](https://github.com/carbon-design-system/carbon/tree/main/packages/icons-react)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jameswlepage) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
