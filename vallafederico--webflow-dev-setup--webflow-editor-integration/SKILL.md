---
name: webflow-editor-integration
description: Handle Webflow Designer/Editor mode vs Published site behavior. Use when detecting the editor, disabling custom animations in the designer, or managing features based on the environment. Use when this capability is needed.
metadata:
  author: vallafederico
---

# Webflow Editor Integration

This skill explains how to ensure your custom code doesn't interfere with the Webflow Designer/Editor while providing full functionality on the published site.

## Editor Detection

Import `handleEditor` from `@webflow/detect-editor`:

```typescript
import { handleEditor } from "@webflow/detect-editor";

export default function (element: HTMLElement, dataset: DOMStringMap) {
  handleEditor((isEditor) => {
    if (isEditor) {
      // Designer Mode: disable interactions, reset animations
      element.style.pointerEvents = "none";
      element.style.transform = "none";
      element.style.opacity = "1";
    } else {
      // Published Mode: enable functionality
      element.style.pointerEvents = "auto";
    }
  });
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vallafederico) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
