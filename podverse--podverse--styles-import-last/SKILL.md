---
name: styles-import-last
description: Enforces import ordering so styles (CSS/SCSS module) imports are last. Use when editing React components or pages in .tsx/.jsx files, or when the user mentions import order or style imports. Use when this capability is needed.
metadata:
  author: podverse
---

# Styles Import Last

ESLint enforces import order (including styles last) via `simple-import-sort/imports`. Run `npm run lint:fix` to fix order.

## Instructions

- In React components and pages (`.tsx`/`.jsx`), place the styles import as the final import at the top of the file.
- Keep existing import grouping, but move the styles import to the end of the import block.
- Applies to CSS/SCSS module imports like `styles` or similar.
- If order is wrong, run `npm run lint:fix` to auto-fix.

## Example

```tsx
import React from 'react';

import { Button } from '../Button/Button';

import styles from './MyComponent.module.scss';
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/podverse) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
