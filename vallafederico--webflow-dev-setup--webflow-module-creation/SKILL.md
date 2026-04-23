---
name: webflow-module-creation
description: Create and register custom JavaScript modules for Webflow projects. Use when creating new functionality, adding data-module attributes, or structuring component logic. Use when this capability is needed.
metadata:
  author: vallafederico
---

# Webflow Module Creation

This skill guides you through creating and registering custom modules that are automatically mounted based on DOM attributes.

## Core Principles

- **File Location**: All modules must be created in `src/modules/`.
- **Naming**: The filename (e.g., `test.ts`) must exactly match the `data-module` attribute value (e.g., `data-module="test"`).
- **Structure**: Every module must have a default export function.

## Module Template

```typescript
import { onMount, onDestroy } from "@/modules/_";

/**
 * @param element The DOM element with the data-module attribute
 * @param dataset The data attributes of the element
 */
export default function (element: HTMLElement, dataset: DOMStringMap) {
  // Setup logic here
  
  onMount(() => {
    // Initialization code
  });

  onDestroy(() => {
    // Cleanup code
  });
}
```

## Step-by-Step Creation

1. **Create the File**: Add a new `.ts` file in `src/modules/` (e.g., `src/modules/my-component.ts`).
2. **Implement the Function**: Export a default function with the signature `(element: HTMLElement, dataset: DOMStringMap) => void`.
3. **Register in HTML**: Add the `data-module="my-component"` attribute to the target element in Webflow/HTML.
4. **Access Data**: Use the `dataset` parameter to access `data-` attributes passed from the DOM.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vallafederico) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
