---
name: frontend-supabase-sdk
description: Use when making direct Supabase SDK calls in TypeScript files (hooks, components, routes, utilities)
metadata:
  author: ntq78
---

# Frontend Supabase SDK Usage

## Naming Convention for SDK Responses

### Pattern

```typescript
const sb_Service[StringParam]_Operation = await supabase.service.method()
```

**Components:**

- `sb_` - Prefix for all Supabase SDK responses
- `Service` - The Supabase service: `Auth`, `From`, `Functions`, `Storage`
- `[StringParam]` - String parameters converted to PascalCase and concatenated directly (no underscore)
- `Operation` - The primary operation: `Select`, `Insert`, `Update`, `Delete`, `Invoke`, etc.

### String Parameter Conversion

**snake_case → PascalCase**

```typescript
"organization_members" → OrganizationMembers
"rel_files_tags" → RelFilesTags
```

**kebab-case → PascalCase**

```typescript
"generate-upload-url" → GenerateUploadUrl
"delete-file" → DeleteFile
```

**Concatenation:** String parameters are concatenated directly with the service/method name (no underscore between them)

### Primary Operation Only

Use only the main operation, ignore modifiers like `.single()`, `.eq()`, `.maybeSingle()`, etc.

```typescript
// ✅ Correct - primary operation only
const sb_FromProjects_Select = await supabase.from("projects").select().eq("id", id).single();

// ❌ Wrong - don't include modifiers
const sb_FromProjects_SelectEqSingle = await supabase
    .from("projects")
    .select()
    .eq("id", id)
    .single();
```

## Core Examples

### Database Operations (from)

```typescript
// Insert
const sb_FromProjects_Insert = await supabase.from("projects").insert(body).select().single();

// Select
const sb_FromOrganizationMembers_Select = await supabase
    .from("organization_members")
    .select("*, organizations(*)")
    .eq("user_id", userId);

// Update
const sb_FromProjects_Update = await supabase
    .from("projects")
    .update(body)
    .eq("id", id)
    .select()
    .single();

// Delete
const sb_FromRelFilesTags_Delete = await supabase
    .from("rel_files_tags")
    .delete()
    .eq("file_id", fileId);
```

### Auth Operations

```typescript
const sb_Auth_GetUser = await supabase.auth.getUser();
const user = sb_Auth_GetUser.data.user;

const sb_Auth_GetSession = await supabase.auth.getSession();
const session = sb_Auth_GetSession.data.session;

const sb_Auth_SignOut = await supabase.auth.signOut();
```

### Functions (Edge Functions)

```typescript
const sb_Functions_InvokeGenerateUploadUrl = await supabase.functions.invoke(
    "generate-upload-url",
    { body: { fileName, projectId } }
);
```

### Storage Operations

```typescript
const sb_StorageProjectFiles_Upload = await supabase.storage
    .from("project-files")
    .upload(path, file);

const sb_StorageProjectFiles_Download = await supabase.storage.from("project-files").download(path);
```

## Accessing Data and Errors

```typescript
const sb_FromProjects_Select = await supabase.from("projects").select();
if (sb_FromProjects_Select.error) {
    console.error(sb_FromProjects_Select.error);
    throw sb_FromProjects_Select.error;
}
const projects = sb_FromProjects_Select.data;
```

## Why This Convention?

### ❌ Problems with Destructuring

**Naming Conflicts:**

```typescript
const { data, error } = await supabase.from("projects").insert();
const { data, error } = await supabase.from("files").select(); // ❌ Conflict!
```

**Ugly Inline Renaming:**

```typescript
const { data: uploadData, error: urlError } =
    await supabase.functions.invoke("generate-upload-url");
const {
    data: { user },
    error: userError,
} = await supabase.auth.getUser();
const { data: metadata, error: metadataError } = await supabase.from("files").insert();
```

**Inconsistent Patterns:**

```typescript
const { data: projects, error: projectsError }; // One dev's choice
const { data: projectData, error: projectErr }; // Another dev's choice
```

### ✅ Benefits of sb\_\* Pattern

- **No Conflicts:** Each variable has unique, descriptive name
- **Explicit and Verbose:** Immediately clear what each variable represents
- **Consistent Convention:** Always follows same pattern - no guessing
- **Better for AI/LLM:** Systematic and reliably followed
- **Self-Documenting:** Variable name describes the operation

## Common Patterns

### Error Handling

```typescript
const sb_FromProjects_Insert = await supabase.from("projects").insert(body);
if (sb_FromProjects_Insert.error) {
    console.error("Failed to create project:", sb_FromProjects_Insert.error);
    throw sb_FromProjects_Insert.error;
}
return sb_FromProjects_Insert.data;
```

### Early Return

```typescript
const sb_Auth_GetUser = await supabase.auth.getUser();
if (sb_Auth_GetUser.error || !sb_Auth_GetUser.data.user) {
    return null;
}
const user = sb_Auth_GetUser.data.user;
```

### Nested Data

```typescript
const sb_Auth_GetSession = await supabase.auth.getSession();
const session = sb_Auth_GetSession.data.session;
```

## Usage in Different Contexts

**In Query Hooks (useQ\_\*):**

```typescript
const queryFn = async (projectId: string) => {
    const sb_FromFiles_Select = await supabase
        .from("files")
        .select("*, rel_files_tags(file_tags(*))")
        .eq("project_id", projectId);

    if (sb_FromFiles_Select.error) throw sb_FromFiles_Select.error;
    return sb_FromFiles_Select.data;
};
```

**In Mutation Hooks (useM\_\*):**

```typescript
const mutationFn = async (body: CreateProjectBody) => {
    const sb_FromProjects_Insert = await supabase.from("projects").insert(body).select().single();

    if (sb_FromProjects_Insert.error) throw sb_FromProjects_Insert.error;
    return sb_FromProjects_Insert.data;
};
```

**In Route Guards:**

```typescript
export const loader = async () => {
    const sb_Auth_GetSession = await supabase.auth.getSession();
    const session = sb_Auth_GetSession.data.session;

    if (!session) {
        throw redirect({ to: "/login" });
    }

    return { session };
};
```

## Related Skills

- **frontend-query-hooks** - Query hooks using this convention
- **frontend-mutation-hooks** - Mutation hooks using this convention
- **frontend-supabase-auth** - Auth-specific patterns

For additional examples: `.claude/skills/frontend-supabase-sdk/examples.md`

<!-- Last compacted: 2025-11-16 -->

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ntq78) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
