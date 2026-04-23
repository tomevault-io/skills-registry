---
name: frontend-mutation-hooks
description: Use when creating or using TanStack Query mutations for data modifications
metadata:
  author: ntq78
---

# Frontend Mutation Hooks Pattern

Clean, minimal mutation hooks for data modifications. NO comments - self-documenting code through naming and types.

## Naming Convention

**Hook:** `useM_[Scope]_[EntityAction]` | **Variable:** `m[EntityAction]` (drop scope)

```typescript
const mProjectCreate = useM_PageOrganization_ProjectCreate();
mProjectCreate.mutation.mutate({ name: "New Project" });
```

**Actions:** Explicit suffix (no underscore): `Create`, `Update`, `Delete`, `Upload`
**Folder:** `hooks/Page[Name]/useM_Page[Name]_[EntityAction].ts`

## Supabase SDK Pattern

```typescript
// ✅ CORRECT - Never destructure
const sb_FromProjects_Insert = await supabase.from("projects").insert();
if (sb_FromProjects_Insert.error) throw sb_FromProjects_Insert.error;
return sb_FromProjects_Insert.data;

// ❌ WRONG
const { data, error } = await supabase.from("projects").insert();
```

## Create Pattern

```typescript
import { useMutation } from "@tanstack/react-query";
import { supabase, type Supabase_InsertDto } from "@/configs/supabase/config";
import useApp from "antd/es/app/useApp";

export type UseM_PageOrganization_ProjectCreate_Params = Pick<
    Supabase_InsertDto<"projects">,
    "organization_id" | "name"
> &
    Partial<Pick<Supabase_InsertDto<"projects">, "description">>;

export const useM_PageOrganization_ProjectCreate = () => {
    const { message } = useApp();

    const mutation = useMutation({
        mutationFn: async (body: UseM_PageOrganization_ProjectCreate_Params) => {
            const sb_FromProjects_Insert = await supabase
                .from("projects")
                .insert(body)
                .select()
                .single();
            if (sb_FromProjects_Insert.error) throw sb_FromProjects_Insert.error;
            return sb_FromProjects_Insert.data;
        },
        onSuccess: () => message.success("Project created successfully!"),
        onError: (error) => {
            console.error("Error creating project:", error);
            message.error("Failed to create project!");
        },
    });

    return { mutation };
};
```

## Update/Delete Pattern

Separates record ID (hook params) from mutable data (mutationFn params):

```typescript
import type { AtLeastOne } from "@/types/utility.types";

export type UseM_PageOrganization_ProjectUpdate_Params = { projectId: string };
export type UseM_PageOrganization_ProjectUpdate_Body = AtLeastOne<
    Pick<Supabase_UpdateDto<"projects">, "name" | "description">
>;

export const useM_PageOrganization_ProjectUpdate = ({
    projectId,
}: UseM_PageOrganization_ProjectUpdate_Params) => {
    const { message } = useApp();

    const mutation = useMutation({
        mutationKey: ["projects", "update", projectId],
        mutationFn: async (body: UseM_PageOrganization_ProjectUpdate_Body) => {
            const sb_FromProjects_Update = await supabase
                .from("projects")
                .update(body)
                .eq("id", projectId)
                .select()
                .single();
            if (sb_FromProjects_Update.error) throw sb_FromProjects_Update.error;
            return sb_FromProjects_Update.data;
        },
        onSuccess: () => message.success("Project updated!"),
        onError: (error) => {
            console.error("Error updating project:", error);
            message.error("Failed to update project!");
        },
    });

    return { mutation };
};
```

| Aspect            | Create         | Update/Delete        |
| ----------------- | -------------- | -------------------- |
| Hook params       | None `()`      | Record ID `({ id })` |
| mutationFn params | Full body      | Only mutable fields  |
| Type exports      | `_Params` only | `_Params` + `_Body`  |
| mutationKey       | Generic        | Includes record ID   |

## Modal Confirmations

```typescript
const { modal } = App.useApp();
modal.confirm({
    title: "Delete File",
    content: `Delete "${fileName}"?`,
    okType: "danger",
    onOk: async () => await mFileDelete.mutation.mutateAsync({ fileId }),
});
```

**mutateAsync:** Use in `modal.confirm()` onOk, sequential operations
**mutate:** Normal button clicks, fire-and-forget

## Invalidation Strategy

- **Org-scoped tables:** NO manual invalidation (realtime sync handles it)
- **Global tables** (`organizations`, `profiles`): MUST manually invalidate

```typescript
onSuccess: () => {
    queryClient.invalidateQueries({ queryKey: QueryKeys.organizations.all() });
},
```

## Error Handling

```typescript
// ❌ WRONG - logging twice
if (error) {
    console.error(error);
    throw error;
} // Logged here
onError: (error) => console.error(error); // And here!

// ✅ CORRECT - throw directly, let onError handle logging
if (error) throw error;
onError: (error) => {
    console.error(error);
    message.error("Failed!");
};
```

## Detection Checklist

- [ ] Hook: `useM_[Scope]_[EntityAction]`
- [ ] Variable: `m[EntityAction]`
- [ ] Returns `{ mutation }` only
- [ ] NO JSDoc, NO inline comments
- [ ] Error handling in `onError` only
- [ ] Uses `App.useApp()` for `message`
- [ ] Update/Delete: Hook takes `({ entityId })`, exports `_Params` + `_Body`, body uses `AtLeastOne`

## Common Mistakes

- ❌ Logging before throwing → ✅ Throw directly, log in onError
- ❌ `mPageOrganization_ProjectCreate` → ✅ `mProjectCreate`
- ❌ Destructuring hooks → ✅ Namespace pattern
- ❌ Manual invalidation for org-scoped → ✅ Realtime handles it
- ❌ ID in mutationFn body → ✅ ID in hook params

<!-- Last compacted: 2026-01-15 -->

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ntq78) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
