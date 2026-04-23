---
name: frontend-query-hooks
description: Use when creating or using TanStack Query hooks for data fetching
metadata:
  author: ntq78
---

# Frontend Query Hooks Pattern

TanStack Query hook patterns for data fetching and state management.

## CRITICAL RULE: NO DESTRUCTURING

```typescript
// ✅ CORRECT - Store entire hook result with short variable name
const qOrganization = useQ_PageOrganization_Organization({ organizationId });
qOrganization.query.isLoading;
qOrganization.organization?.projects;

// ❌ WRONG - Never destructure
const { query, organization } = useQ_PageOrganization_Organization({ organizationId });

// ❌ WRONG - Variable name too verbose
const qPageOrganization_Organization = useQ_PageOrganization_Organization({ organizationId });
```

**Why:** Provides namespace, prevents name conflicts, makes refactoring easier.

## Hook Naming Convention

### Pattern: `useQ_[Scope]_[Entity]`

**Scopes:**

- `Page[Name]` - Page-level data (e.g., `useQ_PageRoot_Organizations`)
- `Page[Name]_[SubComp]` - Sub-component data (e.g., `useQ_PageProjectShot_Files_Files`)
- `Me` - User-specific data (e.g., `useQ_Me`)
- `Tables` - Direct table access (e.g., `useQ_Tables_Organizations`)
- `Options` - Dropdown/select options from database (e.g., `useQ_Options_RolePermissions_Roles`)

**Entity:** Plural for lists (`Organizations`), singular for single items (`Organization`)

**Folder Structure:**

- Page: `hooks/Page[Name]/useQ_Page[Name]_[Entity].ts`
- Sub-component: `hooks/Page[Name]/Page[Name]_[SubComp]/useQ_Page[Name]_[SubComp]_[Entity].ts`
- User: `hooks/Me/useQ_Me.ts`
- Tables: `hooks/Tables/useQ_Tables_[Entity].ts`
- Options: `hooks/Options/useQ_Options_[Table]_[Field]s.ts`

### Variable Naming: `q[Entity]`

```typescript
const qOrganizations = useQ_PageRoot_Organizations();
const qOrganization = useQ_PageOrganization_Organization({ organizationId });
const qMe = useQ_Me();
const qRoles = useQ_Options_RolePermissions_Roles();
```

## Hook Return Structure

```typescript
return {
    query,              // Always return full query object
    [rawData],          // Raw data array or single item
    [derivedMaps],      // Optional: Maps for quick lookup
};
```

**Examples:** `{ query, projects, projectsMap }` | `{ query, project }` | `{ query, options, optionsMap }`

## QueryKeys Pattern - `.list()` vs `.record()`

**CRITICAL:** Use `.list()` for list queries and `.record()` for single-record queries. Enables granular invalidation in realtime sync.

```typescript
import { QueryKeys } from "@/utils/query/queryKeys";

// LIST query - fetches multiple rows, invalidates on ANY table change
queryKey: [...QueryKeys.scenes.list(), { setId }];

// RECORD query - fetches one row by ID, invalidates ONLY when that record changes
queryKey: [...QueryKeys.scenes.record(sceneId)];

// Single record with joins (Case 3: one scene + many files)
queryKey: [
    ...QueryKeys.scenes.record(sceneId),
    ...QueryKeys.scene_files.list(),
    ...QueryKeys.files.list(),
];
```

### Join Strategy

| Case        | Relationship                | Pattern                        |
| ----------- | --------------------------- | ------------------------------ |
| **Case 1**  | A (1) + B (1:1, ID known)   | `A.record(aId), B.record(bId)` |
| **Case 2**  | As (list) + Bs (list)       | `A.list(), B.list()`           |
| **Case 3**  | A (1) + Bs (1:N array)      | `A.record(aId), B.list()`      |
| **Case 1b** | A (1) + B (1:1, ID unknown) | `A.record(aId), B.list()`      |

**Rules:**

1. List queries MUST use `.list()` - fetches multiple rows
2. Single-record queries MUST use `.record(id)` - fetches one row by ID
3. Joined arrays use `.list()` - any change invalidates parent
4. Known 1:1 IDs use `.record()` - if you know the joined ID

## Minimal Processing Principle

**Query hooks should do MINIMAL data transformation. Let components handle business logic.**

### ✅ ALLOWED Processing

- **Null coalescing:** `query.data || []`
- **Map building:** `reduce((acc, item) => ({ ...acc, [item.id]: item }), {})`
- **Simple memoization:** Wrapping raw data in useMemo

### ❌ FORBIDDEN Processing

- Flattening/reshaping data structures
- Extracting nested arrays (e.g., `organization.projects` → `projects`)
- Business logic (isOwner, role, permissions)
- Cross-hook data merging
- Custom type reshaping

### Components Handle Business Logic

```typescript
// ✅ CORRECT - Use raw data directly inline
return (
    <div>
        {qOrganization.organization?.owner_id === qMe.user?.id && <OwnerBadge />}
        <h1>{qOrganization.organization?.name}</h1>
    </div>
);

// ❌ WRONG - Don't create intermediate variables in hooks
const isOwner = useMemo(() => organization?.owner_id === qMe.user?.id, [...]);
```

### DRY for Repeated Checks (3+ times)

```typescript
// ✅ Extract const when same check repeats 3+ times
const isCurrentUserOwner = qOrganization.organization?.owner_id === qMe.user?.id;

// ✅ Helper functions for loops with different parameters
const isUserOwner = (userId: string) => qOrganization.organization?.owner_id === userId;
```

**Rules:** No useMemo for simple booleans, extract at 3+ repetitions, use helper functions in loops.

## Type Safety with QueryData

```typescript
import { supabase, QueryData } from "@/configs/supabase/config";

// 1. Extract query function OUTSIDE hook
const createProjectsQuery = (orgId: string) =>
    supabase
        .from("projects")
        .select("id, name, description, created_at") // Explicit columns only!
        .eq("organization_id", orgId);

// 2. Infer type from query shape
export type PageOrganization_Projects_QueryData = QueryData<ReturnType<typeof createProjectsQuery>>;

// 3. Use in hook
export const useQ_PageOrganization_Organization = ({ organizationId }: Params) => {
    const { message } = useApp();

    const query = useQuery({
        enabled: !!organizationId,
        queryKey: [...QueryKeys.projects.list(), { organizationId }],
        queryFn: async () => {
            const sb_FromProjects_Select = await createProjectsQuery(organizationId);
            if (sb_FromProjects_Select.error) {
                console.error(sb_FromProjects_Select.error);
                message.error("Failed to fetch projects!");
                throw sb_FromProjects_Select.error;
            }
            return sb_FromProjects_Select.data;
        },
    });

    const projects = useMemo(() => query.data || [], [query.data]);
    const projectsMap = useMemo(
        () =>
            projects.reduce(
                (acc, p) => ({ ...acc, [p.id]: p }),
                {} as Record<string, PageOrganization_Projects_QueryData[number]>
            ),
        [projects]
    );

    return { query, projects, projectsMap };
};
```

**Benefits:** Compile-time safety, better performance, self-documenting, refactor-safe.

**JSONB Columns:** Use database override pattern with `MergeDeep` from `type-fest`. See `supabase-schema-design` skill.

## Query Key Factory for Mutation Reuse

Export query key factory when mutations need cache access:

```typescript
// Export factory (NOT a hook)
export const PageSet_SceneFile_QueryKey = (sceneFileId: string) =>
    [...QueryKeys.scene_files.record(sceneFileId), ...QueryKeys.files.list()] as const;

// Use in query hook
queryKey: PageSet_SceneFile_QueryKey(sceneFileId);

// Use in mutation for optimistic updates
import {
    PageSet_SceneFile_QueryKey,
    type PageSet_SceneFile_QueryData,
} from "./useQ_PageSet_SceneFile";
const queryKey = PageSet_SceneFile_QueryKey(variables.id);
queryClient.setQueryData(queryKey, optimisticValue);
```

**Naming:** `useQ_PageSet_SceneFile` → `PageSet_SceneFile_QueryKey`

## Options Query Hooks Pattern

For dropdown/select options from database tables.

### When to Use

- ✅ Options from database (roles, categories, statuses)
- ✅ Options that may change dynamically
- ❌ Static/hardcoded options (use const array)

### Naming: `useQ_Options_[Table]_[Field]s`

```typescript
useQ_Options_RolePermissions_Roles; // Unique roles from role_permissions.role
useQ_Options_Files_MimeTypes; // Unique MIME types from files.mime_type
```

### Return Structure

```typescript
return {
    query, // TanStack Query object
    options, // Array<{ label, value }> for Antd Select
    optionsMap, // Record<value, { label, value }> for lookups
};
```

### Usage

```typescript
const qRoles = useQ_Options_RolePermissions_Roles();
<Select options={qRoles.options} loading={qRoles.query.isLoading} />
```

**Key patterns:** Labels formatted for display, values are raw DB values, use `Array.from(new Set(...))` for unique values.

## Hook Composition

Compose hooks to avoid deeply nested joins:

```typescript
export const useQ_PageRoot_Organizations = () => {
    const qMe = useQ_Me();
    const organizationIds = useMemo(
        () => qMe.user?.organization_memberships?.map((m) => m.organization_id) || [],
        [qMe.user]
    );
    const qTables_Organizations = useQ_Tables_Organizations({ organization_ids: organizationIds });
    // ... combine data
    return { query: qMe.query, organizations, organizationsMap };
};
```

## Profile Batch Query Pattern

**See `frontend-profile-batch-query` skill** for the cross-schema join workaround pattern when fetching multiple user profiles.

## Detection Checklist

**Hook Structure:**

- [ ] Hook name: `useQ_[Scope]_[Entity]`
- [ ] Variable name: `q[Entity]` (drop scope prefix)
- [ ] NO DESTRUCTURING
- [ ] Returns `{ query, [data], [maps] }` only - no business logic
- [ ] Query function extracted outside hook
- [ ] Explicit column selection (NOT `select("*")`)
- [ ] QueryData type exported
- [ ] Uses QueryKeys (`.list()` or `.record()`)
- [ ] Derived data uses useMemo
- [ ] Error handling with `message.error()`
- [ ] Proper `enabled` flag for conditional queries
- [ ] NO flattening/reshaping, NO business logic

**Options Hook:**

- [ ] Name: `useQ_Options_[Table]_[Field]s`
- [ ] Returns `{ query, options, optionsMap }`
- [ ] Options: `Array<{ label: string, value: string }>`
- [ ] Located in `hooks/Options/`

## Common Mistakes

1. ❌ Destructuring hooks → Use namespace pattern
2. ❌ Using `select("*")` → Use explicit columns with QueryData
3. ❌ Business logic in hooks → Move to components
4. ❌ Flattening data → Return raw query.data
5. ❌ Deep Supabase joins → Use hook composition
6. ❌ Missing QueryKeys → Always use `.list()` or `.record()`
7. ❌ Not memoizing → Always memoize derived data

<!-- Last compacted: 2025-12-18 -->

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ntq78) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
