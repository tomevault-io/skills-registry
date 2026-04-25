---
name: supabase-typing-architect
description: | Use when this capability is needed.
metadata:
  author: spectaculous-code
---

# Supabase Typing Architect

Diagnose and fix Supabase TypeScript typing issues. Act as diagnostician + surgeon, not general builder.

**Cross-cutting learnings:** See `.claude/LEARNINGS.md` → "Supabase Type Casts" section for `as any` patterns, when to regenerate types, and proper casting techniques.

## Golden Rules

1. **NEVER modify auto-generated `types.ts`** - It gets overwritten by `supabase gen types`
2. **NEVER add `as any`** unless explicitly requested by user
3. **NEVER duplicate the `Database` type** - Extend it, don't copy it
4. **ALWAYS use typed wrappers** for non-public schema RPC calls

## Project Type Sources

| File | Source | Editable? |
|------|--------|-----------|
| `src/integrations/supabase/types.ts` | `supabase gen types typescript` | NO |
| `src/integrations/supabase/custom-types.ts` | Manual | YES |
| `src/integrations/supabase/client.ts` | Lovable scaffold | Carefully |

## Schema Architecture

This project uses multiple Postgres schemas:

| Schema | Content | In Generated Types? |
|--------|---------|---------------------|
| `public` | User data, app config | YES |
| `bible_schema` | Bible verses, topics, AI | NO (PostgREST exposed) |
| `notifications` | Push notification queue | NO |
| `admin` | Audit logs, widget analytics | NO |
| `feedback` | User feedback system | NO |
| `auth` | Supabase Auth (managed) | Partial |

## Diagnostic Flow

```
Type error on RPC call?
├── RPC in public schema → Check types.ts Functions section
├── RPC in bible_schema → Need typed wrapper (see Pattern A)
├── RPC in other schema → Need typed wrapper (see Pattern A)
└── Table query error → Check if table is in types.ts

Import error?
├── @/integrations/supabase/types not found → Check tsconfig paths
├── Lovable preview fails → Check vite.config.ts resolve.alias
└── Monorepo package can't import → Check package.json exports
```

## Solution Patterns

### Pattern A: Typed RPC Wrapper (for non-public schemas)

When RPC functions exist in bible_schema or other non-public schemas:

```typescript
// src/integrations/supabase/custom-types.ts

export interface GetVerseStudyDataParams {
  p_osis: string;
  p_version_code?: string;
}

export interface VerseStudyData {
  verse_id: string;
  text_content: string;
  book_name: string;
  chapter_number: number;
  verse_number: number;
}

// Usage with type assertion (safe because we control the RPC):
const { data } = await (supabase.rpc as any)('get_verse_study_data', params) as {
  data: VerseStudyData | null;
  error: Error | null
};
```

### Pattern B: Schema-Specific Client Helper

For frequently-used non-public schema operations:

```typescript
// src/lib/bibleSchemaClient.ts
import { supabase } from '@/integrations/supabase/client';

export async function callBibleSchemaRpc<T>(
  functionName: string,
  params: Record<string, unknown>
): Promise<{ data: T | null; error: Error | null }> {
  return (supabase.rpc as any)(functionName, params);
}
```

### Pattern C: REST API with Schema Header

For direct table access in non-public schemas:

```typescript
const response = await fetch(`${SUPABASE_REST_URL}/table_name`, {
  headers: {
    'Accept-Profile': 'bible_schema',
    'apikey': SUPABASE_PUBLISHABLE_KEY,
  },
});
```

## Common Issues & Fixes

### Issue: `as any` spreading in codebase

**Diagnosis:**
```bash
grep -r "as any" src/ --include="*.ts" --include="*.tsx" | grep -i supabase
```

**Fix:** Replace each `as any` with proper typed wrapper (Pattern A or B)

### Issue: Types regenerated, custom types lost

**Fix:**
1. Regenerate: `npx supabase gen types typescript --project-id xxx > types.ts`
2. Move custom types to custom-types.ts
3. Update imports

### Issue: Lovable preview can't resolve types

**Fix:** Ensure vite.config.ts has:
```typescript
resolve: {
  alias: {
    '@': path.resolve(__dirname, './src'),
  },
},
```

## Type Regeneration

```bash
npx supabase gen types typescript --project-id iryqgmjauybluwnqhxbg > apps/raamattu-nyt/src/integrations/supabase/types.ts
```

## Anti-Patterns

| Don't | Do Instead |
|-------|------------|
| Edit types.ts | Add to custom-types.ts |
| Copy Database type | Import and extend it |
| Use `as any` without comment | Use typed wrapper with explicit return type |
| Create duplicate supabase clients | Import from @/integrations/supabase/client |

## References

- [references/type-inventory.md](references/type-inventory.md) - Complete type source inventory
- [references/rpc-type-map.md](references/rpc-type-map.md) - RPC function signatures

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spectaculous-code) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
