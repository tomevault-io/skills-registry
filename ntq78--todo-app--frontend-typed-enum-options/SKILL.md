---
name: frontend-typed-enum-options
description: Use when creating SELECT, Radio, or dropdown options for database columns with fixed values - ALWAYS use instead of hardcoding options arrays
metadata:
  author: ntq78
---

# Frontend: Typed Enum Options

Static, type-safe enum options derived from Supabase database enums. Provides compile-time safety ensuring UI options always match database schema.

## When to Use

- ✅ Fixed schema enums (not admin-configurable)
- ✅ Options need display metadata (labels, colors, descriptions)
- ✅ Type safety required
- ❌ Dynamically fetched from DB rows → use `useQ_Options_*` hooks
- ❌ Values change per organization → use query hooks

## CRITICAL: New Database Columns with Fixed Values

When creating a new column with fixed values (status, type, mode, etc.):

1. **Database**: Create PostgreSQL ENUM type (see `supabase-schema-design`)
2. **Frontend**: Create `src/types/{table}.options.ts`
3. **Component**: Import options, NEVER hardcode

```typescript
// Step 1: Migration
CREATE TYPE scene_file_keyframes_interpolation_mode_enum AS ENUM ('hold', 'linear');

// Step 2: src/types/scene_file_keyframes.options.ts
import { Supabase_Enums } from "@/configs/supabase/config";
import { Utils_Options_EnumsToOptions } from "@/utils/options/EnumsToOptions";

const SceneFileKeyframes_InterpolationMode: Record<
    Supabase_Enums<"scene_file_keyframes_interpolation_mode_enum">,
    { value: Supabase_Enums<"scene_file_keyframes_interpolation_mode_enum">; label: string }
> = {
    hold: { value: "hold", label: "Hold" },
    linear: { value: "linear", label: "Linear" },
};

export const Options_SceneFileKeyframes = {
    interpolation_mode: Utils_Options_EnumsToOptions(SceneFileKeyframes_InterpolationMode),
};

// Step 3: Component usage
<Select options={Options_SceneFileKeyframes.interpolation_mode.options} />
```

## File Organization

**Pattern:** `src/types/{table}.options.ts` → `Options_{PascalTableName}`

```
src/types/
├── files.options.ts                    → Options_Files
├── organization_members.options.ts     → Options_OrganizationMembers
└── scene_file_keyframes.options.ts     → Options_SceneFileKeyframes
```

## Core Pattern

```typescript
import { Supabase_Enums } from "@/configs/supabase/config";
import { Utils_Options_EnumsToOptions } from "@/utils/options/EnumsToOptions";

const TableName_ColumnName: Record<
    Supabase_Enums<"enum_name">,
    { value: Supabase_Enums<"enum_name">; label: string /* metadata */ }
> = {
    enum_value_1: { value: "enum_value_1", label: "Display Label 1" },
    enum_value_2: { value: "enum_value_2", label: "Display Label 2" },
};

export const Options_TableName = {
    columnName: Utils_Options_EnumsToOptions(TableName_ColumnName),
};
```

**Return Structure:**

```typescript
{ options: T[], map: Record<string, T> }  // .options for Select, .map for O(1) lookups
```

## Shared Enum Pattern

When enums (like `app_role`) are used by multiple tables:

**Primary owner defines:**

```typescript
// organization_members.options.ts
export const Options_OrganizationMembers = {
    role: Utils_Options_EnumsToOptions(OrganizationMembers_Role),
};
```

**Secondary tables import & re-export:**

```typescript
// organization_invitations.options.ts
import { Options_OrganizationMembers } from "./organization_members.options";

export const Options_OrganizationInvitations = {
    status: Utils_Options_EnumsToOptions(OrganizationInvitations_Status),
    role: Options_OrganizationMembers.role, // Re-export
};
```

## Metadata Types

Extend base option type with domain-specific metadata:

| Field               | Type        | Use Case          | Component           |
| ------------------- | ----------- | ----------------- | ------------------- |
| `color`             | `string`    | Status indicators | `Tag`, `Badge`      |
| `description`       | `string`    | Tooltips          | `Tooltip`, `Select` |
| `icon`              | `ReactNode` | Menu items        | `Menu`, `Button`    |
| `allowedExtensions` | `string[]`  | File validation   | File upload         |

```typescript
// Example: Status with color
const Status: Record<Supabase_Enums<"invitation_status">, { value: ...; label: string; color: string }> = {
    pending: { value: "pending", label: "Pending", color: "warning" },
    accepted: { value: "accepted", label: "Accepted", color: "success" },
};

// Example: Category with extensions
const Category: Record<Supabase_Enums<"files_category_enum">, { value: ...; label: string; allowedExtensions: string[] }> = {
    location: { value: "location", label: "Location", allowedExtensions: [".ply", ".splat"] },
    object: { value: "object", label: "Object", allowedExtensions: [".usd", ".usdc"] },
};
```

## Usage Examples

```typescript
// Select component
<Select options={Options_OrganizationMembers.role.options} />

// Tag with map lookup (O(1))
const statusMeta = Options_OrganizationInvitations.status.map[status];
<Tag color={statusMeta.color}>{statusMeta.label}</Tag>

// Validation
const categoryMeta = Options_Files.category.map[category];
categoryMeta.allowedExtensions.includes(extension);
```

## Static vs Query Options

| Scenario             | Static (.options.ts) | Query (useQ*Options*\*) |
| -------------------- | -------------------- | ----------------------- |
| Fixed schema enum    | ✅                   | ❌                      |
| Admin-configurable   | ❌                   | ✅                      |
| Realtime updates     | ❌                   | ✅                      |
| Performance critical | ✅                   | ❌                      |

## Type Safety Guarantee

When Supabase enum changes, TypeScript enforces updating the mapping:

```bash
pnpm sb:types  # Regenerate types after ALTER TYPE ... ADD VALUE
```

```
Error: Type '{ admin: {...}; user: {...} }' is missing: moderator
```

**Build fails until all enum values have labels/metadata.**

## Detection Checklist

- [ ] File: `src/types/{table}.options.ts`
- [ ] Export: `Options_{PascalTableName}`
- [ ] Uses `Supabase_Enums<"enum_name">` for type safety
- [ ] Uses `Utils_Options_EnumsToOptions` utility
- [ ] Shared enums imported from primary owner (no duplication)
- [ ] NO hardcoded option arrays in components

## Common Mistakes

| Wrong                                  | Correct                               |
| -------------------------------------- | ------------------------------------- |
| Hardcoding options in component        | Import from `Options_*`               |
| Duplicating shared enum definitions    | Import & re-export from primary owner |
| Using query hooks for fixed enums      | Use static options (no network)       |
| Direct union types `"admin" \| "user"` | Use `Supabase_Enums<"enum_name">`     |

## Related Skills

- **supabase-schema-design** - Creating enums in migrations
- **frontend-antd-components** - Using with Select, Radio, Tag

<!-- Last compacted: 2025-12-18 -->

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ntq78) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
