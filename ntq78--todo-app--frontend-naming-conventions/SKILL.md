---
name: frontend-naming-conventions
description: Use when creating or naming pages, components, stores, hooks, routes, or utilities
metadata:
  author: ntq78
---

# Frontend: Naming Conventions

Strict naming conventions for all frontend code entities.

## Pages

**Pattern:** `Page_[Name]`

```tsx
// Folder: src/pages/Page_Organization/
// File: Page_Organization.tsx
export const Page_Organization = () => <div>Organization page</div>;
```

**Rules:** Folder `Page_[Name]`, File `Page_[Name].tsx`, ONLY `export const` (NO default exports)

**Examples:** `Page_Root`, `Page_Login`, `Page_Organization`, `Page_AccessDenied`

## Subcomponents (Page-Specific)

**Pattern:** `Page[Name]_[ComponentName]` (remove underscore after "Page")

```tsx
// ✅ Correct
(PageProjectShot_Files, PageProjectShot_Canvas, PageOrganization_Header);

// ❌ Wrong
ProjectShot_Files; // Missing "Page" prefix
Page_ProjectShot_Files; // Extra underscore after Page
```

**Folder:** `src/pages/Page_ProjectShot/PageProjectShot_Files/PageProjectShot_Files.tsx`

## Nested Subcomponents (Three-Level)

When a subcomponent is ONLY used by another subcomponent:

**Pattern:** `Page[Name]_[ParentComponent]_[ChildComponent]`

```tsx
PageOrganization_WorkspaceProjects_ProjectCard; // Used only by WorkspaceProjects
PageProject_SetCard_ActionsMenu; // Used only by SetCard
```

**Folder:** `Page_Organization/PageOrganization_WorkspaceProjects/PageOrganization_WorkspaceProjects_ProjectCard/`

## Stores (TanStack Store)

**Pattern:** `Store_[Name]`

```tsx
// File: src/stores/Store_App.ts
class Store_App_Default {
    /* ... */
}
export const Store_App = new Store_App_Default();
export const useStore_App = () => useStore(Store_App);
```

## Services (Singleton Classes)

**Use for:** Platform APIs (AudioContext, WASM), performance-critical caches, RAF loop data

**Pattern:** `service_[Scope]_[Name]` | **Variable:** `s[Name]`

| Entity    | Pattern                             | Example                               |
| --------- | ----------------------------------- | ------------------------------------- |
| File      | `service_[Scope]_[Name].ts`         | `service_PageScene_WebAudio.ts`       |
| Class     | `Service_[Scope]_[Name]_Class`      | `Service_PageScene_WebAudio_Class`    |
| Singleton | `service_[Scope]_[Name]`            | `service_PageScene_WebAudio`          |
| Types     | `Service_[Scope]_[Name]_[TypeName]` | `Service_PageScene_WebAudio_ClipData` |

```typescript
// File: pages/Page_Scene/service_PageScene_WebAudio.ts

// Types - exported with full prefix
export interface Service_PageScene_WebAudio_ClipData {
    clipId: string;
    fileId: string;
}

// Class - internal naming with full prefix
class Service_PageScene_WebAudio_Class {
    private buffers = new Map<string, AudioBuffer>();

    syncPlayback(clips: Service_PageScene_WebAudio_ClipData[], time: number): void {
        // Called from RAF loop
    }
}

// Singleton export - matches file name
export const service_PageScene_WebAudio = new Service_PageScene_WebAudio_Class();

// Usage in components
const sWebAudio = service_PageScene_WebAudio;
sWebAudio.syncPlayback(clips, time);
```

### Location Rule

Same as hooks/utils: **colocate at closest common parent**

- If only `Page_Scene` children use it → `pages/Page_Scene/`
- If multiple pages use it → `src/services/` (global)

### When to Use Service vs Hook

| Use Service                          | Use Hook                       |
| ------------------------------------ | ------------------------------ |
| Platform APIs (AudioContext, WASM)   | React state management         |
| Binary data caching (blobs, buffers) | Data fetching (TanStack Query) |
| RAF loop access (60fps reads)        | UI state subscriptions         |
| Imperative operations                | Declarative patterns           |

## Query Hooks (TanStack Query)

**Hook:** `useQ_[Scope]_[Entity]` | **Variable:** `q[Entity]` (drop scope prefix)

### Scope follows component hierarchy

| Location            | Scope Pattern                 | Example                               |
| ------------------- | ----------------------------- | ------------------------------------- |
| Page root           | `Page[Name]`                  | `useQ_PageScene_Scene`                |
| Subcomponent        | `Page[Name]_[Subcomponent]`   | `useQ_PageScene_Script_Script`        |
| Nested subcomponent | `Page[Name]_[Parent]_[Child]` | `useQ_PageScene_Timeline_Track_Clips` |

**CRITICAL:** Scope must match the folder/subcomponent hierarchy. A hook in `PageScene_Decks/` folder MUST use scope `PageScene_Decks`:

```tsx
// ✅ Correct - scope matches folder location
// File: Page_Scene/PageScene_Decks/useQ_PageScene_Decks_Decks.ts
export const useQ_PageScene_Decks_Decks = () => { ... };

// ❌ Wrong - scope doesn't include subcomponent
// File: Page_Scene/PageScene_Decks/useQ_PageScene_Decks.ts
export const useQ_PageScene_Decks = () => { ... };  // Missing "_Decks" entity!
```

### Variable naming

```tsx
// ✅ Correct - short variable name
const qOrganization = useQ_PageOrganization_Organization({ organizationId });
qOrganization.query.isLoading;
qOrganization.organization?.projects;

// ❌ Wrong - destructuring (NEVER do this)
const { query, organization } = useQ_PageOrganization_Organization();

// ❌ Wrong - verbose variable name
const qPageOrganization_Organization = useQ_PageOrganization_Organization();
```

### Folder placement

| Hook Location | Folder                                                                   |
| ------------- | ------------------------------------------------------------------------ |
| Page-level    | `Page_[Name]/useQ_Page[Name]_[Entity].ts`                                |
| Subcomponent  | `Page_[Name]/Page[Name]_[Subcomp]/useQ_Page[Name]_[Subcomp]_[Entity].ts` |

## Mutation Hooks (TanStack Query)

**Hook:** `useM_[Scope]_[EntityAction]` | **Variable:** `m[EntityAction]`

Scope follows the same hierarchy rules as Query Hooks (see above).

```tsx
// ✅ Correct - subcomponent scope
// File: Page_Scene/PageScene_Decks/useM_PageScene_Decks_DeckShotCreate.ts
const mDeckShotCreate = useM_PageScene_Decks_DeckShotCreate();
mDeckShotCreate.mutation.mutate({ deck_id: "..." });

// ❌ Wrong - missing subcomponent in scope
// File: Page_Scene/PageScene_Decks/useM_PageScene_DeckShotCreate.ts
const mDeckShotCreate = useM_PageScene_DeckShotCreate(); // Should be _Decks_DeckShotCreate
```

## Junction/Relation Table Hooks

For hooks that query or mutate junction tables (many-to-many relationships):

**Pattern:** `$Table1$Table2$Relation` (use actual table names, NOT invented entity names)

**Query:** `useQ_[Scope]_$Table1$Table2$Relation`
**Mutation:** `useM_[Scope]_$Table1$Table2$Relation[Action]`
**Variable:** `qRelations` / `mRelationCreate`

```tsx
// ✅ Correct - uses actual table names
useQ_PageScene_Timeline_SceneAudioTracks$SceneFiles$Relation;
useM_PageScene_Timeline_SceneAudioTracks$SceneFiles$RelationCreate;

// ❌ Wrong - invented entity name
useQ_PageScene_Timeline_AudioClips; // "AudioClip" is not a table
useM_PageScene_Timeline_AudioClipCreate;
```

**Why:** Don't invent entity names. Use actual table names to maintain traceability between hooks and database schema.

## Provider Hooks (React Context)

**Hook:** `useProvider_[Name]` | **Variable:** `p[Name]`

```tsx
const pAssetManager = useProvider_Spark_AssetManagerModal();
pAssetManager.state.projectId;
pAssetManager.setState({ projectId: "123" });

const pTheme = useProvider_Theme();
pTheme.toggleTheme();
```

## Hotkey Hooks

**Pattern:** `useHotkeys_[ComponentName]` - standalone files, colocated with component

```tsx
// File: useHotkeys_PageScene_Timeline.ts
import { useHotkeys } from "react-hotkeys-hook";

export const useHotkeys_PageScene_Timeline = () => {
    const pPageScene = useProvider_Page_Scene();

    useHotkeys(
        "space",
        () => {
            pPageScene.setState((prev) => ({
                timeline_isPlaying: !prev.timeline_isPlaying,
            }));
        },
        { preventDefault: true }
    );

    useHotkeys(
        "escape",
        () => {
            pPageScene.setState({ nodeSelected: null });
        },
        { enableOnFormTags: true }
    );
};

// Usage - no return, just call it
useHotkeys_PageScene_Timeline();
```

**Rules:** Standalone file, use `react-hotkeys-hook`, no returns (side-effect only)

**Implementation details:** See [frontend-hotkeys](../frontend-hotkeys/SKILL.md) for patterns, options, and anti-patterns.

## Routes (TanStack Router)

```
src/routes/__root.tsx           // Root layout (double underscore)
src/routes/_protected.tsx       // Protected layout (single underscore)
src/routes/_protected/$organizationId.tsx  // Dynamic param
src/routes/login.tsx            // Public route
```

## UI Components (Pure, Reusable)

**Pattern:** `UI_[ComponentName]`

```tsx
// Folder: src/components/UI_HorizontalNav/UI_HorizontalNav.tsx
export const UI_HorizontalNav = () => <nav>...</nav>;
```

**Requirements:** Pure (no external context), reusable, ANTD-based

## Utility Functions

### Placement Hierarchy

| Scope          | When                                        | Pattern                   | Location                |
| -------------- | ------------------------------------------- | ------------------------- | ----------------------- |
| **Global**     | Different domains (auth + data, infra + UI) | `Utils_[Category]_[Name]` | `src/utils/`            |
| **Domain**     | 2+ consumers under common parent            | `utils_[Scope]_[Name].ts` | Closest common parent   |
| **Single-use** | One consumer                                | `const camelCase`         | Inline in consumer file |

```typescript
// Global - used across domains
export const Utils_Files_FormatSize = (bytes: number): string => { ... };

// Domain - used by multiple Timeline components
// File: Page_Scene/PageScene_Timeline/utils_PageScene_Timeline_SnapToGrid.ts
export const utils_PageScene_Timeline_SnapToGrid = (time: number): number => { ... };

// Single-use - inline before component
const calculateTickInterval = (pps: number) => { ... };
export const PageScene_Timeline_Ruler = () => { /* uses calculateTickInterval */ };
```

### Decision Tree

- Used across different domains? → Global `src/utils/Utils_Category_Name`
- Used by only ONE consumer? → Inline in consumer file
- Otherwise → Domain-scoped at closest common parent

## Constants (Static Values)

**Use for:** Static values that don't change - dimensions, px values, text, arrays, options, object maps, configs

**Pattern:** `const_[Scope]` | **Export:** `const_[Scope]_[Name]`

| Entity | Pattern                | Example                                   |
| ------ | ---------------------- | ----------------------------------------- |
| File   | `const_[Scope].ts`     | `const_PageTestScriptEditor.ts`           |
| Export | `const_[Scope]_[Name]` | `const_PageTestScriptEditor_HeaderHeight` |

```typescript
// File: pages/Page_TestScriptEditor/const_PageTestScriptEditor.ts

// Dimensions
export const const_PageTestScriptEditor_HeaderHeight = 48;
export const const_PageTestScriptEditor_SidebarWidth = 240;

// Options (for selects/dropdowns)
export const const_PageTestScriptEditor_ZoomLevels = [0.5, 1, 2, 4] as const;

// Text
export const const_PageTestScriptEditor_EmptyStateText = "No scripts found";

// Object maps
export const const_PageTestScriptEditor_BlockTypeLabels = {
    scene: "Scene Heading",
    action: "Action",
    dialogue: "Dialogue",
} as const;
```

### Subcomponent Constants

For subcomponent-scoped constants, follow the subcomponent naming pattern:

```typescript
// File: pages/Page_Scene/PageScene_Timeline/const_PageScene_Timeline.ts
export const const_PageScene_Timeline_TrackHeight = 32;
export const const_PageScene_Timeline_RulerHeight = 24;
export const const_PageScene_Timeline_MinZoom = 0.1;
export const const_PageScene_Timeline_MaxZoom = 10;
```

### Placement Rules (Decision Tree)

| Condition                        | Action                          |
| -------------------------------- | ------------------------------- |
| Used in ONE file + small         | Inline `const` in consumer file |
| Used in MULTIPLE files           | Extract to `const_` file        |
| Large value (even if single-use) | Extract to `const_` file        |

**"Large value"** = long text strings, big arrays (5+ items), objects with multiple keys

```typescript
// ✅ Inline - small, single-use
const PADDING = 8;
export const PageScene_Timeline_Ruler = () => {
    /* uses PADDING */
};

// ✅ Extract - used by multiple files
// const_PageScene_Timeline.ts
export const const_PageScene_Timeline_TrackHeight = 32;

// ✅ Extract - large object, keeps component file clean
// const_PageScene_Timeline.ts
export const const_PageScene_Timeline_InterpolationOptions = [
    { value: "hold", label: "Hold" },
    { value: "linear", label: "Linear" },
    { value: "ease", label: "Ease In/Out" },
    { value: "bezier", label: "Bezier" },
] as const;
```

### Usage Pattern

```typescript
// Import with namespace for clarity
import {
    const_PageScene_Timeline_TrackHeight,
    const_PageScene_Timeline_RulerHeight,
} from "./const_PageScene_Timeline";

// Use directly (already namespaced via prefix)
const style = { height: const_PageScene_Timeline_TrackHeight };
```

### Anti-Patterns

```typescript
// ❌ Wrong - no scope prefix
export const HEADER_HEIGHT = 48;

// ❌ Wrong - SCREAMING_SNAKE_CASE (use PascalCase for suffix)
export const const_PageScene_Timeline_TRACK_HEIGHT = 32;

// ❌ Wrong - large object inlined in component file
const PageScene_Timeline = () => {
    const INTERPOLATION_OPTIONS = [
        /* 10 items */
    ]; // Should be in const_ file
};

// ❌ Wrong - shared constant not extracted
// File A uses TRACK_HEIGHT, File B uses TRACK_HEIGHT
// Both define it locally instead of sharing via const_ file
```

## Modal Components

**Pattern:** Modal suffix appended directly (NO underscore before Modal)

```tsx
// ✅ Correct
(Spark_AssetManagerModal, PageRoot_OrganizationComposerModal);

// ❌ Wrong
Spark_AssetManager_Modal; // Extra underscore
```

**Exception:** `Spark_Modal` (utility wrapper)

## Export Pattern

**CRITICAL: All exports must use `export const` - NO default exports**

```tsx
// ✅ Correct
export const Page_Organization = () => { ... };
export const useQ_Me = () => { ... };

// ❌ Wrong
function Page_Organization() { ... }
export { Page_Organization };
export default Page_Organization;
```

## Anti-Pattern Detection

| Wrong                                        | Correct                                                 |
| -------------------------------------------- | ------------------------------------------------------- |
| `export const Organization = ()`             | `export const Page_Organization = ()`                   |
| `const { query } = useQ_Me()`                | `const qMe = useQ_Me()`                                 |
| `const qPageOrganization_Organization = ...` | `const qOrganization = ...`                             |
| `useOrganizations()`                         | `useQ_Me_Organizations()`                               |
| `export default`                             | `export const` only                                     |
| Single-use util in standalone file           | Inline in consumer                                      |
| Domain util in global `src/utils/`           | Place at common parent                                  |
| `webAudioManager` (generic name)             | `service_PageScene_WebAudio`                            |
| Service in `features/shared/services/`       | Service at closest common parent                        |
| `AudioClipPlaybackData` (generic type)       | `Service_PageScene_WebAudio_ClipData`                   |
| `export const HEADER_HEIGHT = 48`            | `const_[Scope]_HeaderHeight`                            |
| Large inline arrays/objects in component     | Extract to `const_` file                                |
| Same constant defined in multiple files      | Share via `const_` file                                 |
| Hook in subcomponent missing scope segment   | `useQ_PageScene_Decks_Decks` not `useQ_PageScene_Decks` |

## Anti-Pattern: Single-Function Hooks

**Never create a hook that only returns one function:**

```tsx
// ❌ Wrong - hook returns single function
const useSomething_Zoom = () => {
    const handleZoomWheel = () => { ... };
    return { handleZoomWheel };
};

// ✅ Correct - define inline where used
const Component = () => {
    useEffect(() => {
        const handleZoomWheel = (e: WheelEvent) => { ... };
        el.addEventListener("wheel", handleZoomWheel);
        return () => el.removeEventListener("wheel", handleZoomWheel);
    }, []);
};
```

<!-- Last updated: 2026-01-29 - Added subcomponent scope hierarchy clarification for Query/Mutation Hooks to prevent missing scope segment errors -->

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ntq78) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
