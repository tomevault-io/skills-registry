---
name: gen-hook
description: Generate React Query hooks for data operations (queries and mutations). Creates type-safe hooks for fetching, creating, updating, and deleting data with proper cache invalidation. Use when this capability is needed.
metadata:
  author: ramakrishnan24689
---

# Generate React Query Hooks

Generate React Query hooks for data fetching and mutations with proper cache management and TypeScript types.

## What This Skill Does

1. Creates query hooks for data fetching (use<Feature>, use<Feature>ById)
2. Creates mutation hooks for data modification (useCreate<Feature>, useUpdate<Feature>, useDelete<Feature>)
3. Implements proper query key hierarchy (factory pattern)
4. Sets up cache invalidation after mutations
5. Provides TypeScript types and error handling
6. Includes optimistic updates pattern (advanced)

## Usage

```
/gen-hook todos
/gen-hook accounts
/gen-hook userprofile
```

## Template

This skill uses a comprehensive React Query template:

- **templates/react-query-hooks.template.ts** - Complete hooks with query key factory, cache management, and optimistic updates

**How to use template**: Read the template to understand the complete pattern, then generate adapted code based on the actual service layer you're integrating with.

## Workflow

### Step 1: Verify Service Layer Exists

Before generating hooks, ensure the service layer exists:

```bash
ls src/features/$ARGUMENTS[0]/services/*Service.ts
```

Read the service file to understand:
- Available methods (getTodos, createTodo, etc.)
- DTO types (CreateTodoDto, UpdateTodoDto)
- Domain model type (Todo)

### Step 2: Read the React Query Template

```
Read templates/react-query-hooks.template.ts
```

Study the template structure:
- Query key factory pattern
- Query hooks with proper configuration
- Mutation hooks with cache invalidation
- Optimistic updates pattern
- TypeScript types

### Step 3: Generate Hooks File Following Template Pattern

Create `src/features/<feature>/hooks/use<Feature>.ts` following the template structure.

## React Query Hook Pattern (From Template)

### Critical Pattern #1: Query Key Factory (From Template)

The template shows the query key factory pattern for hierarchical cache management:

```typescript
// Query key factory - adapt to your feature name
const <feature>Keys = {
  all: ['<feature>'] as const,
  lists: () => [...<feature>Keys.all, 'list'] as const,
  list: (filters?: Filters) => [...<feature>Keys.lists(), filters] as const,
  details: () => [...<feature>Keys.all, 'detail'] as const,
  detail: (id: string) => [...<feature>Keys.details(), id] as const,
};
```

**Why this matters**: Hierarchical keys allow precise cache invalidation.

### Critical Pattern #2: Query Hooks with Proper Configuration

```typescript
export const use<Features> = (filters?: <Feature>Filters) => {
  return useQuery({
    queryKey: <feature>Keys.list(filters), // Use factory
    queryFn: () => <Feature>Service.get<Features>(filters),
    staleTime: 5 * 60 * 1000, // 5 minutes
    retry: 1,
  });
};

export const use<Feature>ById = (id: string) => {
  return useQuery({
    queryKey: <feature>Keys.detail(id), // Use factory
    queryFn: () => <Feature>Service.get<Feature>ById(id),
    enabled: !!id, // ✅ Only run if ID exists
    staleTime: 5 * 60 * 1000,
    retry: 1,
  });
};
```

### Critical Pattern #3: Mutation Hooks with Cache Invalidation

```typescript
export const useCreate<Feature> = () => {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: (dto: Create<Feature>Dto) =>
      <Feature>Service.create<Feature>(dto),

    onSuccess: () => {
      // ✅ Invalidate all list queries
      queryClient.invalidateQueries({ queryKey: <feature>Keys.lists() });
    },

    onError: (error) => {
      console.error('Failed to create:', error);
    },
  });
};

export const useUpdate<Feature> = () => {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: ({ id, dto }: { id: string; dto: Update<Feature>Dto }) =>
      <Feature>Service.update<Feature>(id, dto),

    onSuccess: (_, variables) => {
      // ✅ Invalidate both list and specific detail
      queryClient.invalidateQueries({ queryKey: <feature>Keys.lists() });
      queryClient.invalidateQueries({ queryKey: <feature>Keys.detail(variables.id) });
    },
  });
};

export const useDelete<Feature> = () => {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: (id: string) => <Feature>Service.delete<Feature>(id),

    onSuccess: (_, id) => {
      queryClient.invalidateQueries({ queryKey: <feature>Keys.lists() });
      queryClient.removeQueries({ queryKey: <feature>Keys.detail(id) }); // ✅ Remove from cache
    },
  });
};
```

### Critical Pattern #4: Optimistic Updates (Advanced)

The template includes an optimistic update pattern for better UX:

```typescript
export const useUpdate<Feature>Optimistic = () => {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: ({ id, dto }) => <Feature>Service.update<Feature>(id, dto),

    onMutate: async ({ id, dto }) => {
      // Cancel outgoing refetches
      await queryClient.cancelQueries({ queryKey: <feature>Keys.detail(id) });

      // Snapshot previous value
      const previous = queryClient.getQueryData(<feature>Keys.detail(id));

      // Optimistically update
      queryClient.setQueryData(
        <feature>Keys.detail(id),
        (old) => old ? { ...old, ...dto } : old
      );

      return { previous, id };
    },

    onError: (err, variables, context) => {
      // Rollback on error
      if (context?.previous) {
        queryClient.setQueryData(<feature>Keys.detail(context.id), context.previous);
      }
    },

    onSettled: (_, __, variables) => {
      // Always refetch to ensure consistency
      queryClient.invalidateQueries({ queryKey: <feature>Keys.detail(variables.id) });
      queryClient.invalidateQueries({ queryKey: <feature>Keys.lists() });
    },
  });
};
```

### Step 4: Verify Against Template Checklist

Before completing, verify your generated hooks include:

- ✅ Query key factory with hierarchical structure
- ✅ All necessary imports from @tanstack/react-query
- ✅ Imports from service layer (service and types)
- ✅ Query hooks with proper staleTime and retry config
- ✅ Mutation hooks with cache invalidation in onSuccess
- ✅ Enabled flag in conditional queries (useById)
- ✅ Error handling in mutation onError
- ✅ TypeScript types for all functions
- ✅ Optional: Optimistic updates pattern included

### Step 5: Test Hooks Integration

Verify hooks work with service layer:
```bash
# TypeScript check
npx tsc --noEmit

# Check imports resolve
grep "import.*Service" src/features/<feature>/hooks/use<Feature>.ts
```

## Usage in Components (Examples)

```typescript
function FeatureList() {
  const { data: features, isLoading, error, refetch } = useFeatures();
  const createFeature = useCreateFeature();
  const updateFeature = useUpdateFeature();
  const deleteFeature = useDeleteFeature();

  if (isLoading) return <Spinner label="Loading..." />;
  if (error) return <MessageBar intent="error">Failed to load</MessageBar>;

  return (
    <div>
      {features?.map(feature => (
        <Card key={feature.id}>{feature.name}</Card>
      ))}
    </div>
  );
}
```

## Workflow

1. Read service file to understand available methods
2. Create hooks directory: `src/features/<feature>/hooks/`
3. Generate use<Feature>.ts with all hooks
4. Add TypeScript imports and exports
5. Validate TypeScript compilation

## Output

```
✅ React Query hooks generated!

📁 Created: src/features/<feature>/hooks/use<Feature>.ts

🎣 Generated Hooks:
   - use<Features>(): Query hook for fetching multiple
   - use<Feature>ById(id): Query hook for single record
   - useCreate<Feature>(): Mutation hook for creating
   - useUpdate<Feature>(): Mutation hook for updating
   - useDelete<Feature>(): Mutation hook for deleting

💡 Usage in components:
   const { data, isLoading } = use<Features>();
   const create = useCreate<Feature>();
   await create.mutateAsync(newData);
```

## Related Skills

- **/gen-service**: Generate service layer first
- **/gen-component**: Generate UI components using these hooks

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ramakrishnan24689) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
