---
name: rac-to-shadcn
description: Convert React Aria Components (RAC) starter kit to use shadcn color tokens and build a shadcn-compatible registry. Use when setting up a new RAC + shadcn project, converting RAC components to shadcn tokens, or building a component registry. Use when this capability is needed.
metadata:
  author: luisrudge
---

# RAC to shadcn Conversion

Convert React Aria Components to use shadcn's CSS variable-based color system and create a shadcn-compatible registry.

## Overview

This skill guides you through:

1. Setting up a Vite + React + Tailwind v4 project
2. Downloading RAC starter components
3. Converting colors from Tailwind classes to shadcn CSS variables
4. Building a shadcn-compatible registry
5. Creating demo pages for each component

## Prerequisites

- Node.js 18+
- bun (preferred) or npm

## Project Setup

### 1. Create Vite Project

```bash
bun create vite my-rac-shadcn --template react-ts
cd my-rac-shadcn
bun install
```

### 2. Install Dependencies

```bash
# Core dependencies
bun add react-aria-components react-stately tailwind-variants tailwind-merge lucide-react

# Dev dependencies
bun add -D tailwindcss @tailwindcss/vite tailwindcss-react-aria-components tailwindcss-animate
```

### 3. Configure Tailwind v4

Create `src/index.css`:

```css
@import "tailwindcss";
@plugin "tailwindcss-react-aria-components";
@plugin "tailwindcss-animate";

@custom-variant dark (&:is(.dark *));

@theme inline {
  --color-background: var(--background);
  --color-foreground: var(--foreground);
  --color-card: var(--card);
  --color-card-foreground: var(--card-foreground);
  --color-popover: var(--popover);
  --color-popover-foreground: var(--popover-foreground);
  --color-primary: var(--primary);
  --color-primary-foreground: var(--primary-foreground);
  --color-secondary: var(--secondary);
  --color-secondary-foreground: var(--secondary-foreground);
  --color-muted: var(--muted);
  --color-muted-foreground: var(--muted-foreground);
  --color-accent: var(--accent);
  --color-accent-foreground: var(--accent-foreground);
  --color-destructive: var(--destructive);
  --color-border: var(--border);
  --color-input: var(--input);
  --color-ring: var(--ring);
  --radius-sm: calc(var(--radius) - 4px);
  --radius-md: calc(var(--radius) - 2px);
  --radius-lg: var(--radius);
}

:root {
  --background: oklch(1 0 0);
  --foreground: oklch(0.145 0 0);
  --card: oklch(1 0 0);
  --card-foreground: oklch(0.145 0 0);
  --popover: oklch(1 0 0);
  --popover-foreground: oklch(0.145 0 0);
  --primary: oklch(0.205 0 0);
  --primary-foreground: oklch(0.985 0 0);
  --secondary: oklch(0.97 0 0);
  --secondary-foreground: oklch(0.205 0 0);
  --muted: oklch(0.97 0 0);
  --muted-foreground: oklch(0.556 0 0);
  --accent: oklch(0.97 0 0);
  --accent-foreground: oklch(0.205 0 0);
  --destructive: oklch(0.58 0.22 27);
  --border: oklch(0.922 0 0);
  --input: oklch(0.922 0 0);
  --ring: oklch(0.708 0 0);
  --radius: 0.625rem;
}

.dark {
  --background: oklch(0.145 0 0);
  --foreground: oklch(0.985 0 0);
  --card: oklch(0.205 0 0);
  --card-foreground: oklch(0.985 0 0);
  --popover: oklch(0.205 0 0);
  --popover-foreground: oklch(0.985 0 0);
  --primary: oklch(0.87 0 0);
  --primary-foreground: oklch(0.205 0 0);
  --secondary: oklch(0.269 0 0);
  --secondary-foreground: oklch(0.985 0 0);
  --muted: oklch(0.269 0 0);
  --muted-foreground: oklch(0.708 0 0);
  --accent: oklch(0.371 0 0);
  --accent-foreground: oklch(0.985 0 0);
  --destructive: oklch(0.704 0.191 22.216);
  --border: oklch(1 0 0 / 10%);
  --input: oklch(1 0 0 / 15%);
  --ring: oklch(0.556 0 0);
}

@layer base {
  * {
    @apply border-border;
  }
  body {
    @apply bg-background text-foreground;
  }
}
```

### 4. Download RAC Starter Components

```bash
mkdir -p .rac-original
curl -o .rac-original/manifest.json "https://react-spectrum.adobe.com/manifest.tailwind.json"
# Download each component listed in manifest
```

## Color Conversion Rules

### Primary Mappings

| RAC Class                    | shadcn Token              |
| ---------------------------- | ------------------------- |
| `bg-blue-600`                | `bg-primary`              |
| `bg-blue-700`                | `bg-primary/90`           |
| `bg-blue-500`                | `bg-primary/80`           |
| `text-white` (on primary bg) | `text-primary-foreground` |
| `text-blue-600`              | `text-primary`            |
| `bg-gray-100`                | `bg-secondary`            |
| `bg-gray-200`                | `bg-accent`               |
| `text-gray-500`              | `text-muted-foreground`   |
| `text-gray-700`              | `text-foreground`         |
| `text-gray-900`              | `text-foreground`         |
| `border-gray-300`            | `border-border`           |
| `border-gray-200`            | `border-input`            |
| `bg-red-600`                 | `bg-destructive`          |
| `text-red-600`               | `text-destructive`        |

### Remove Dark Mode Prefixes

Remove ALL `dark:` prefixes - CSS variables handle dark mode automatically:

- `dark:bg-gray-800` → remove entirely
- `dark:text-white` → remove entirely
- `dark:border-gray-600` → remove entirely

### Focus Ring

Use `outline-primary` for focus rings:

```typescript
export const focusRing = tv({
  base: "outline-primary outline outline-offset-2",
  variants: {
    isFocusVisible: {
      false: "outline-0",
      true: "outline-2",
    },
  },
});
```

### Destructive Buttons

Always use `text-white` for destructive buttons (not `text-primary-foreground`):

```typescript
destructive: "bg-destructive hover:bg-destructive/90 text-white";
```

## Component Conversion Process

For each component file:

1. **Read the original** from `.rac-original/`
2. **Apply color mappings** using the table above
3. **Remove dark: prefixes** entirely
4. **Update imports** to use local utils
5. **Save to** `src/components/ui/`

### Example Conversion

**Before (RAC):**

```typescript
const styles = tv({
  base: "bg-blue-600 text-white hover:bg-blue-700 dark:bg-blue-500",
});
```

**After (shadcn):**

```typescript
const styles = tv({
  base: "bg-primary text-primary-foreground hover:bg-primary/90",
});
```

## Building the Registry

### Registry Script

Create `scripts/build-registry.ts`:

```typescript
import * as fs from "fs";
import * as path from "path";

const COMPONENTS_DIR = "src/components/ui";
const OUTPUT_DIR = "registry";

interface RegistryItem {
  $schema: string;
  name: string;
  type: string;
  dependencies: string[];
  registryDependencies: string[];
  files: { path: string; type: string; content: string }[];
}

function buildRegistry() {
  fs.mkdirSync(OUTPUT_DIR, { recursive: true });

  const files = fs
    .readdirSync(COMPONENTS_DIR)
    .filter((f) => f.endsWith(".tsx") || f.endsWith(".ts"));

  for (const file of files) {
    const name = path.basename(file, path.extname(file));
    const content = fs.readFileSync(path.join(COMPONENTS_DIR, file), "utf-8");

    const item: RegistryItem = {
      $schema: "https://ui.shadcn.com/schema/registry-item.json",
      name,
      type: "registry:ui",
      dependencies: extractDependencies(content),
      registryDependencies: extractRegistryDeps(content),
      files: [
        {
          path: `ui/${file}`,
          type: "registry:ui",
          content,
        },
      ],
    };

    fs.writeFileSync(path.join(OUTPUT_DIR, `${name}.json`), JSON.stringify(item, null, 2));
  }
}

function extractDependencies(content: string): string[] {
  const deps: string[] = ["react-aria-components"];
  if (content.includes("tailwind-variants")) deps.push("tailwind-variants");
  if (content.includes("tailwind-merge")) deps.push("tailwind-merge");
  if (content.includes("lucide-react")) deps.push("lucide-react");
  return deps;
}

function extractRegistryDeps(content: string): string[] {
  const deps: string[] = [];
  const importRegex = /from ['"]\.\/(\w+)['"]/g;
  let match;
  while ((match = importRegex.exec(content))) {
    deps.push(match[1].toLowerCase());
  }
  return deps;
}

buildRegistry();
```

Add to `package.json`:

```json
{
  "scripts": {
    "build:registry": "bun run scripts/build-registry.ts"
  }
}
```

## Hosting the Registry

### Option 1: Direct URL

Deploy your app and use full URLs:

```bash
npx shadcn@latest add "https://rac-shadcn.luis-9a1.workers.dev/registry/button.json"
```

### Option 2: Custom Registry Namespace

Users add to their `components.json`:

```json
{
  "registries": {
    "@rac-shadcn": "https://rac-shadcn.luis-9a1.workers.dev/registry/{name}.json"
  }
}
```

Then install:

```bash
npx shadcn@latest add @rac-shadcn/button
```

## React Aria Official Components

These are the official RAC components to include:

1. Autocomplete
2. Breadcrumbs
3. Button
4. Calendar
5. Checkbox
6. CheckboxGroup
7. ColorArea
8. ColorField
9. ColorPicker
10. ColorSlider
11. ColorSwatch
12. ColorSwatchPicker
13. ColorWheel
14. ComboBox
15. DateField
16. DatePicker
17. DateRangePicker
18. Disclosure
19. DisclosureGroup
20. DropZone
21. FileTrigger
22. Form
23. GridList
24. Group
25. Link
26. ListBox
27. Menu
28. Meter
29. Modal
30. NumberField
31. Popover
32. ProgressBar
33. RadioGroup
34. RangeCalendar
35. SearchField
36. Select
37. Separator
38. Slider
39. Switch
40. Table
41. Tabs
42. TagGroup
43. TextField
44. TimeField
45. Toast
46. ToggleButton
47. ToggleButtonGroup
48. Toolbar
49. Tooltip
50. Tree
51. Virtualizer

## Common Issues & Fixes

### ListBox Overflow

Add `overflow-auto` to ListBox base styles.

### Toast Not Showing

Add `<MyToastRegion />` to root layout.

### ToggleButton Gap

Wrap text in `<span>` when combining with icons.

### Tree Not Opening

Destructure props properly in TreeItem to avoid passing children to wrong element.

### Virtualizer Performance

Use `ListLayout` with fixed `rowHeight` for best performance.

## Verification Checklist

- [ ] `bun run type-check` passes
- [ ] `bun run build:app` succeeds
- [ ] All components render in light mode
- [ ] All components render in dark mode
- [ ] Focus rings are visible
- [ ] `bun run build:registry` creates JSON files
- [ ] Registry JSON files are valid

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/luisrudge) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
