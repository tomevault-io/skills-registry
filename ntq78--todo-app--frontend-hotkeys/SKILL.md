---
name: frontend-hotkeys
description: Use when creating or modifying keyboard shortcuts/hotkeys in frontend code
metadata:
  author: ntq78
---

# Frontend: Hotkeys Implementation

Use `react-hotkeys-hook` for all keyboard shortcuts.

**Naming convention:** See [frontend-naming-conventions](../frontend-naming-conventions/SKILL.md) for the `useHotkeys_[ComponentName]` pattern.

## Installation

```bash
pnpm add react-hotkeys-hook
```

## Basic Usage

```typescript
import { useHotkeys } from "react-hotkeys-hook";

export const useHotkeys_PageScene = () => {
    const pPageScene = useProvider_Page_Scene();

    // Simple hotkey (blocked in inputs by default)
    useHotkeys(
        "space",
        () => {
            pPageScene.setState((prev) => ({
                timeline_isPlaying: !prev.timeline_isPlaying,
            }));
        },
        { preventDefault: true }
    );

    // Escape - ALWAYS use enableOnFormTags: true
    useHotkeys(
        "escape",
        () => {
            pPageScene.setState({ nodeSelected: null });
        },
        { enableOnFormTags: true }
    );

    // Conditional hotkey (check inside callback)
    useHotkeys(
        "f",
        () => {
            if (pPageScene.state.nodeSelected?.category !== "shot") return;
            pPageScene.togglePreviewFullscreenRef.current?.();
        },
        { preventDefault: true }
    );
};
```

## Options Reference

| Option                    | Default | Description                                      |
| ------------------------- | ------- | ------------------------------------------------ |
| `preventDefault`          | `false` | Prevent browser default (use for Space, F, etc.) |
| `enableOnFormTags`        | `false` | Allow in input/textarea/select                   |
| `enableOnContentEditable` | `false` | Allow in contentEditable elements                |
| `enabled`                 | `true`  | Conditionally enable/disable the hotkey          |
| `keyup`                   | `false` | Trigger on keyup instead of keydown              |
| `keydown`                 | `true`  | Trigger on keydown                               |

## Rules

### 1. Default Input Protection

Hotkeys are **blocked in input/textarea/select** by default. This is the desired behavior.

```typescript
// This will NOT fire when user is typing in an input
useHotkeys("space", () => togglePlayback());
```

### 2. Escape Exception

Escape should **always work**, even in inputs. Always add `enableOnFormTags: true`:

```typescript
// ✅ Escape works everywhere
useHotkeys("escape", () => cancelAction(), { enableOnFormTags: true });
```

### 3. Prevent Browser Defaults

For keys with browser defaults (Space scrolls, F can trigger form), use `preventDefault`:

```typescript
useHotkeys('space', () => { ... }, { preventDefault: true });
useHotkeys('f', () => { ... }, { preventDefault: true });
```

### 4. Dependencies

**RULE: If you read external values, you MUST use `useHotkeysWithDeps` to declare deps.**

```typescript
import { useHotkeysWithDeps } from "@/hooks/hotkeys";

// ✅ Reading external values - MUST use useHotkeysWithDeps
useHotkeysWithDeps(
    () => {
        if (nodeSelected?.category === "shot") {
            deleteKeyframes(nodeSelected.selectedKeyframeIds);
        }
    },
    [nodeSelected, deleteKeyframes], // ESLint enforces this
    "delete"
);
```

**RULE: If you have NO external dependencies, use native `useHotkeys`.**

```typescript
import { useHotkeys } from "react-hotkeys-hook";

// ✅ No external deps - use native useHotkeys with functional update
useHotkeys(
    "space",
    () => {
        pPageScene.setState((prev) => ({
            timeline_isPlaying: !prev.timeline_isPlaying,
        }));
    },
    { preventDefault: true }
);
```

**Signature comparison:**

```typescript
// Native (no deps):
useHotkeys(keys, callback, options);

// With deps (ESLint enforced):
useHotkeysWithDeps(callback, deps, keys, options);
```

| Scenario                               | Hook to Use                              |
| -------------------------------------- | ---------------------------------------- |
| Reading external state/values          | `useHotkeysWithDeps` (MUST declare deps) |
| Functional update only (`prev => ...`) | `useHotkeys` (no deps needed)            |
| Calling functions with external IDs    | `useHotkeysWithDeps` (MUST declare deps) |

### 5. Conditional Enabling

Two patterns for conditional hotkeys:

```typescript
// Pattern A: Check inside callback (simpler)
useHotkeys("f", () => {
    if (nodeSelected?.category !== "shot") return;
    toggleFullscreen();
});

// Pattern B: Use enabled option (prevents callback entirely)
useHotkeys(
    "f",
    () => toggleFullscreen(),
    {
        enabled: nodeSelected?.category === "shot",
    },
    [nodeSelected]
);
```

Use Pattern A for simple checks. Use Pattern B when the check itself is expensive.

### 6. Location

Colocate hotkey hooks with their component:

```
Page_Scene/
├── Page_Scene.tsx                    # calls useHotkeys_PageScene()
├── useHotkeys_PageScene.ts           # Space, Escape, Delete, F
└── PageScene_Timeline/
    ├── PageScene_Timeline.tsx        # calls useHotkeys_PageScene_Timeline()
    └── useHotkeys_PageScene_Timeline.ts  # Delete for keyframes
```

## Anti-Patterns

```typescript
// ❌ Wrong - manual listener (old pattern)
useEffect(() => {
    const handler = (e: KeyboardEvent) => {
        if (e.code === 'Space') { ... }
    };
    document.addEventListener('keydown', handler);
    return () => document.removeEventListener('keydown', handler);
}, []);

// ✅ Correct - use library
useHotkeys('space', () => { ... }, { preventDefault: true });
```

```typescript
// ❌ Wrong - forgetting enableOnFormTags for Escape
useHotkeys("escape", () => cancel());

// ✅ Correct - Escape works in inputs
useHotkeys("escape", () => cancel(), { enableOnFormTags: true });
```

```typescript
// ❌ Wrong - reading external value without declaring deps
const [count, setCount] = useState(0);
useHotkeys("space", () => console.log(count)); // count is stale!

// ✅ Correct - reading external value, MUST use useHotkeysWithDeps
useHotkeysWithDeps(() => console.log(count), [count], "space");

// ✅ Correct - no external deps, use native useHotkeys
useHotkeys("space", () => setCount((prev) => prev + 1));
```

## NOT For Continuous Input

Do NOT use `react-hotkeys-hook` for:

- **Camera movement** (WASD held down for continuous movement)
- **Game-style input** (tracking key state in refs for `useFrame`)

These need manual `keydown`/`keyup` listeners to track held keys:

```typescript
// This is CORRECT for continuous input (FlyControls, etc.)
const keys = useRef({ w: false, a: false, s: false, d: false });

useEffect(() => {
    const handleKeyDown = (e: KeyboardEvent) => {
        const key = e.key.toLowerCase();
        if (key in keys.current) keys.current[key] = true;
    };
    const handleKeyUp = (e: KeyboardEvent) => {
        const key = e.key.toLowerCase();
        if (key in keys.current) keys.current[key] = false;
    };
    document.addEventListener("keydown", handleKeyDown);
    document.addEventListener("keyup", handleKeyUp);
    return () => {
        document.removeEventListener("keydown", handleKeyDown);
        document.removeEventListener("keyup", handleKeyUp);
    };
}, []);

useFrame(() => {
    if (keys.current.w) camera.position.add(forward);
    // ...
});
```

## Multi-Key Shortcuts

```typescript
// Modifier + key
useHotkeys("ctrl+s", () => save(), { preventDefault: true });
useHotkeys("cmd+s", () => save(), { preventDefault: true }); // macOS

// Multiple keys (comma-separated)
useHotkeys("ctrl+s, cmd+s", () => save(), { preventDefault: true });

// Array syntax
useHotkeys(["ctrl+z", "cmd+z"], () => undo());
```

## Scopes (Advanced)

Not used initially. Add if needed for complex modal/state management:

```typescript
import { HotkeysProvider, useHotkeysContext } from 'react-hotkeys-hook';

// Wrap app
<HotkeysProvider initiallyActiveScopes={['global']}>
    <App />
</HotkeysProvider>

// Define scoped hotkey
useHotkeys('space', () => play(), { scopes: 'timeline' });

// Enable/disable scopes
const { enableScope, disableScope } = useHotkeysContext();
disableScope('timeline'); // Space no longer triggers
```

---

<!-- Last updated: 2026-01-21 -->

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ntq78) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
