---
name: add-ui-component
description: Add new UI components following shadcn/ui patterns with Radix primitives, Tailwind CSS, and TypeScript interfaces. Use when creating buttons, dialogs, forms, or custom React components. Use when this capability is needed.
metadata:
  author: polaris340
---

# Add UI Component

Guide for adding new UI components to Zennavi.

## Adding a Base shadcn/ui Component

Install from the shadcn/ui registry:

```bash
bunx shadcn@latest add <component-name>
```

Components are installed to `components/ui/`. Do not manually edit these files.

## Creating a Custom Component

### Step 1: Choose the Location

| Directory | Purpose |
|-----------|---------|
| `components/chat/` | Chat interface (messages, input, toolbar) |
| `components/settings/` | Settings forms, credential management, model config |
| `components/conversations/` | Conversation sidebar and list |
| `components/ui/` | Base shadcn/ui only (do not add custom components here) |

### Step 2: Define Props Interface

```typescript
interface MyComponentProps {
  className?: string;
  // ... other props with explicit types
}
```

### Step 3: Create the Component

```typescript
import { cn } from "~/lib/utils";

const MyComponent = (props: MyComponentProps) => {
  return (
    <div className={cn("base-tailwind-classes", props.className)}>
      {/* content */}
    </div>
  );
};

export { MyComponent };
export type { MyComponentProps };
```

Key patterns:
- Access props via `props.x` (not destructured in parameter)
- Use `cn()` for className merging
- Named exports only (no default export)
- Export types separately

### Step 4: Use shadcn/ui Primitives

Import from `~/components/ui/`:

```typescript
import { Button } from "~/components/ui/button";
import { Label } from "~/components/ui/label";
import { Checkbox } from "~/components/ui/checkbox";
```

Icons from lucide-react:

```typescript
import { Plus, Settings, ArrowLeft } from "lucide-react";
```

## Creating a Custom Hook

Location: `lib/hooks/use-*.ts`

```typescript
import { useState, useCallback } from "react";

export function useMyFeature() {
  const [state, setState] = useState(initialValue);

  const action = useCallback(() => {
    // ...
  }, []);

  return { state, action };
}

export type UseMyFeatureReturn = ReturnType<typeof useMyFeature>;
```

Export hooks from `lib/hooks/index.ts`.

## Database Writes

Use fire-and-forget pattern for non-critical DB writes:

```typescript
// No await — write happens in background
db.messages.add({ content, role: "user", conversationId });
```

## Browser Extension Constraints

- No SSR — components only render in the extension side panel
- No Node.js modules (fs, path, crypto, etc.)
- All persistent data uses IndexedDB via Dexie.js
- No `window.fetch` for cross-origin in content scripts (use background service worker)

## Reference Files

- `components/chat/chat-container.tsx` — simple component with cn() and named export
- `components/settings/settings-view.tsx` — complex component with state, overlays, and hook composition
- `components/settings/credential-form.tsx` — form component with validation
- `lib/hooks/use-chat.ts` — complex hook with streaming logic
- `lib/hooks/use-credentials.ts` — CRUD hook with Dexie.js

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/polaris340) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
