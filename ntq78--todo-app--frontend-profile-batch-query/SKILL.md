---
name: frontend-profile-batch-query
description: Use when fetching user profiles in bulk (workaround for PostgREST cross-schema join limitation)
metadata:
  author: ntq78
---

# Profile Batch Query Pattern

Pattern for fetching multiple user profiles when PostgREST cannot join across schema boundaries.

## Why This Pattern Exists

**Technical Constraint:**

- `profiles.id` → `auth.users(id)` (FK to `auth` schema)
- `organization_members.user_id` → `auth.users(id)` (FK to `auth` schema)
- **PostgREST cannot join tables across schema boundaries**
- No direct FK between `profiles` and `organization_members`

**Failed approach:**

```typescript
// ❌ IMPOSSIBLE - PostgREST cannot traverse auth.users
supabase.from("organization_members").select(`
        id,
        user_id,
        profiles (  // ❌ No direct FK relationship
            email
        )
    `);
```

**Error:** `Could not find a relationship between 'organization_members' and 'profiles' in the schema cache`

**Working approach:**

```typescript
// ✅ Batch query using .in() operator
supabase.from("profiles").select("id, email, whitelisted").in("id", userIds);
```

## When to Use

- ✅ Displaying user emails in tables or lists
- ✅ Any multi-user scenario needing profile data
- ✅ Replacing failed PostgREST joins with profiles
- ❌ Single isolated profile (use singular hook instead)

## Complete Implementation

### Hook Structure

**File:** `hooks/[Scope]/useQ_[Scope]_Profiles.ts`

```typescript
import { useQuery } from "@tanstack/react-query";
import { useMemo } from "react";
import { supabase, QueryData } from "@/configs/supabase/config";
import { QueryKeys } from "@/utils/query/queryKeys";
import useApp from "antd/es/app/useApp";

export type UseQ_[Scope]_Profiles_Params = {
    userIds: string[];
    enabled?: boolean;
};

const createProfilesQuery = (userIds: string[]) =>
    supabase
        .from("profiles")
        .select(`
            id,
            email,
            whitelisted,
            whitelisted_at,
            created_at,
            updated_at
        `)
        .in("id", userIds);

export type [Scope]_Profiles_QueryData = QueryData<ReturnType<typeof createProfilesQuery>>;
export type [Scope]_Profile = [Scope]_Profiles_QueryData[number];

export const useQ_[Scope]_Profiles = (params: UseQ_[Scope]_Profiles_Params) => {
    const { message } = useApp();
    const { userIds, enabled = true } = params;

    const query = useQuery({
        enabled: enabled && userIds.length > 0,
        queryKey: [...QueryKeys.profiles.all(), { userIds: [...userIds].sort() }] as const,
        queryFn: async () => {
            if (userIds.length === 0) return [];

            const sb_FromProfiles_Select = await createProfilesQuery(userIds);

            if (sb_FromProfiles_Select.error) {
                console.error(sb_FromProfiles_Select.error);
                message.error("Failed to fetch user profiles!");
                throw sb_FromProfiles_Select.error;
            }

            return sb_FromProfiles_Select.data;
        },
    });

    const profiles = useMemo(() => query.data || [], [query.data]);

    const profilesMap = useMemo(
        () =>
            profiles.reduce(
                (acc, profile) => {
                    acc[profile.id] = profile;
                    return acc;
                },
                {} as Record<string, [Scope]_Profile>
            ),
        [profiles]
    );

    return {
        query,
        profiles,
        profilesMap,
    };
};
```

### Component Usage

```typescript
import { useMemo } from "react";
import { Table } from "antd";
import { useQ_[Scope]_ParentData } from "@/hooks/...";
import { useQ_[Scope]_Profiles } from "@/hooks/...";

function MyComponent() {
    // Step 1: Fetch parent data with user_ids
    const qParent = useQ_[Scope]_ParentData({ ... });

    // Step 2: Extract user IDs
    const userIds = useMemo(
        () => qParent.items.map(item => item.user_id),
        [qParent.items]
    );

    // Step 3: Batch fetch profiles
    const qProfiles = useQ_[Scope]_Profiles({
        userIds,
        enabled: !qParent.query.isLoading
    });

    // Step 4: Loading states
    if (qParent.query.isLoading || qProfiles.query.isLoading) {
        return <Spin />;
    }

    if (qParent.query.error || qProfiles.query.error) {
        return <Alert type="error" message="Failed to load data" />;
    }

    // Step 5: Use profilesMap for O(1) lookups
    return (
        <Table
            dataSource={qParent.items}
            columns={[
                {
                    title: "Email",
                    render: (_, record) => {
                        const profile = qProfiles.profilesMap[record.user_id];
                        return <span>{profile?.email || "Loading..."}</span>;
                    },
                },
            ]}
        />
    );
}
```

## Key Implementation Details

### 1. Enabled Flag Strategy

```typescript
const qProfiles = useQ_[Scope]_Profiles({
    userIds,
    enabled: !qParent.query.isLoading  // Wait for parent to load
});
```

**Why:** Prevents firing query before userIds array is populated

### 2. Query Key with Sorted IDs

```typescript
queryKey: [...QueryKeys.profiles.all(), { userIds: [...userIds].sort() }];
```

**Why:** Same set of user IDs (different order) should hit same cache

### 3. Empty Array Guard

```typescript
if (userIds.length === 0) return [];
```

**Why:** Prevents invalid `.in("id", [])` query to database

### 4. ProfilesMap for Performance

```typescript
const profilesMap = useMemo(
    () =>
        profiles.reduce(
            (acc, profile) => ({ ...acc, [profile.id]: profile }),
            {} as Record<string, Profile>
        ),
    [profiles]
);
```

**Why:** O(1) lookup performance in renders instead of O(n) with `.find()`

## Performance Characteristics

- **Queries:** 2 total (parent + batch profiles)
- **Lookup:** O(1) with profilesMap
- **Cache:** Single cache entry per unique user set
- **Network:** Single batch request for all profiles
- **Comparison to N+1:** 100x faster for 100 users

## Reference Implementation

**Hook:** `src/hooks/PageOrganization/PageOrganization_SettingsUsers/useQ_PageOrganizationSettingsUsers_Profiles.ts`

**Component:** `src/pages/Page_Organization/PageOrganization_SettingsUsers.tsx`

## Detection Checklist

- [ ] Hook name: `useQ_[Scope]_Profiles` (plural)
- [ ] Parameters: `{ userIds: string[], enabled?: boolean }`
- [ ] Uses `.in("id", userIds)` query
- [ ] Returns `{ query, profiles, profilesMap }`
- [ ] Query key includes sorted userIds
- [ ] Handles empty userIds gracefully
- [ ] Component extracts userIds with useMemo
- [ ] Component passes enabled flag
- [ ] Component uses profilesMap for lookups

## Common Mistakes

1. ❌ Attempting PostgREST join - Will always fail
2. ❌ N+1 queries in loops - Performance disaster
3. ❌ Missing enabled flag - Query fires too early
4. ❌ Not sorting userIds - Cache misses
5. ❌ Using .find() instead of profilesMap - O(n) vs O(1)

## Related Skills

- **frontend-query-hooks** - Full query hooks pattern documentation
- **frontend-supabase-sdk** - Supabase SDK naming conventions
- **supabase-schema-design** - Why profiles references auth.users

<!-- Last compacted: 2025-11-18 -->

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ntq78) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
