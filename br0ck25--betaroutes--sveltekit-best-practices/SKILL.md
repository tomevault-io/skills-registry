---
name: sveltekit-best-practices
description: Expert guidance for SvelteKit 5 development with runes, server-side rendering, routing, and performance optimization. Use when working with SvelteKit projects, Svelte 5 runes, +page.svelte files, +server.ts endpoints, or asking about "SvelteKit patterns", "Svelte 5", "runes", "SvelteKit routing", or "SSR". Use when this capability is needed.
metadata:
  author: br0ck25
---

# SvelteKit Best Practices Skill

## Purpose
Provides expert guidance for building production-grade SvelteKit applications with Svelte 5, focusing on:
- Proper use of runes ($state, $derived, $effect, $props)
- File-based routing conventions
- Server/client separation
- Performance optimization
- Type safety

## SvelteKit File Conventions

### Route Files
```
src/routes/
├── +page.svelte          # Page component
├── +page.server.ts       # Server-side page load
├── +page.ts              # Universal page load (runs on both)
├── +layout.svelte        # Layout component
├── +layout.server.ts     # Server-side layout load
├── +server.ts            # API endpoint (GET, POST, etc.)
└── +error.svelte         # Error boundary
```

### Naming Rules (CRITICAL)
- **Page components:** `+page.svelte` (not `page.svelte` or `Page.svelte`)
- **Server endpoints:** `+server.ts` (not `server.ts` or `api.ts`)
- **Layout files:** `+layout.svelte` (not `layout.svelte`)
- **Error boundaries:** `+error.svelte` (not `error.svelte`)

**The `+` prefix is required** - SvelteKit won't recognize files without it.

## Svelte 5 Runes

### $state - Reactive State
```typescript
// ✅ Correct: Use $state for reactive values
let count = $state(0);
let user = $state({ name: 'Alice', age: 30 });

// ✅ Correct: Update directly
count = count + 1;
user.age = 31;

// ❌ Wrong: Don't use old reactive declarations
let count = 0; // Not reactive in Svelte 5
$: doubled = count * 2; // Old syntax
```

### $derived - Computed Values
```typescript
// ✅ Correct: Derive values from state
let count = $state(0);
let doubled = $derived(count * 2);
let isEven = $derived(count % 2 === 0);

// ✅ Correct: Complex derivations
let users = $state([/* ... */]);
let activeUsers = $derived(users.filter(u => u.active));
let userCount = $derived(users.length);

// ❌ Wrong: Don't use regular variables for computed
let doubled = count * 2; // Won't react to changes
```

### $effect - Side Effects
```typescript
// ✅ Correct: Run code when dependencies change
let count = $state(0);

$effect(() => {
  console.log(`Count is now: ${count}`);
  document.title = `Count: ${count}`;
});

// ✅ Correct: Cleanup function
$effect(() => {
  const interval = setInterval(() => {
    count++;
  }, 1000);
  
  return () => clearInterval(interval); // Cleanup
});

// ❌ Wrong: Don't use onMount for reactive updates
onMount(() => {
  // This only runs once, won't react to changes
  console.log(count);
});
```

### $props - Component Props
```typescript
// ✅ Correct: Define props with $props
interface Props {
  name: string;
  age: number;
  optional?: string;
}

const { name, age, optional = 'default' }: Props = $props();

// ✅ Correct: Use destructuring with defaults
const { 
  items = [], 
  onSelect 
}: { 
  items?: Item[]; 
  onSelect: (item: Item) => void;
} = $props();

// ❌ Wrong: Don't use export let
export let name; // Old Svelte 4 syntax
```

## Page Component Pattern

### Typical +page.svelte Structure
```svelte
<script lang="ts">
  import { goto } from '$app/navigation';
  import { page } from '$app/stores';
  import type { PageData } from './$types';
  
  // 1. Props from load function
  interface Props {
    data: PageData;
  }
  const { data }: Props = $props();
  
  // 2. Local state
  let searchQuery = $state('');
  let selectedItems = $state(new Set<string>());
  
  // 3. Derived state
  let filteredItems = $derived(
    data.items.filter(item => 
      item.name.toLowerCase().includes(searchQuery.toLowerCase())
    )
  );
  
  // 4. Actions/handlers
  function handleSelect(id: string) {
    if (selectedItems.has(id)) {
      selectedItems.delete(id);
    } else {
      selectedItems.add(id);
    }
    selectedItems = new Set(selectedItems); // Trigger reactivity
  }
  
  // 5. Effects (if needed)
  $effect(() => {
    // Save search to localStorage
    localStorage.setItem('lastSearch', searchQuery);
  });
</script>

<!-- 6. Template -->
<div class="container">
  <input bind:value={searchQuery} placeholder="Search..." />
  
  {#each filteredItems as item (item.id)}
    <button onclick={() => handleSelect(item.id)}>
      {item.name}
    </button>
  {/each}
</div>
```

## Server vs Client

### When to Use +page.server.ts
Use for:
- Database queries
- API calls with secrets
- Server-only logic
- Authentication checks

```typescript
// src/routes/dashboard/+page.server.ts
import type { PageServerLoad } from './$types';
import { getTrips } from '$lib/server/tripService';

export const load: PageServerLoad = async ({ locals }) => {
  const userId = locals.user?.id;
  if (!userId) throw redirect(303, '/login');
  
  const trips = await getTrips(userId);
  
  return {
    trips,
    user: locals.user
  };
};
```

### When to Use +page.ts
Use for:
- Client-side data fetching
- Universal logic (runs on both)
- No secrets needed

```typescript
// src/routes/blog/+page.ts
import type { PageLoad } from './$types';

export const load: PageLoad = async ({ fetch }) => {
  const response = await fetch('/api/posts');
  const posts = await response.json();
  
  return { posts };
};
```

## API Endpoints (+server.ts)

### Standard Pattern
```typescript
// src/routes/api/trips/+server.ts
import { json, error } from '@sveltejs/kit';
import type { RequestHandler } from './$types';

export const GET: RequestHandler = async ({ locals, url }) => {
  const userId = locals.user?.id;
  if (!userId) throw error(401, 'Unauthorized');
  
  const startDate = url.searchParams.get('start');
  const endDate = url.searchParams.get('end');
  
  const trips = await getTrips(userId, { startDate, endDate });
  
  return json(trips);
};

export const POST: RequestHandler = async ({ request, locals }) => {
  const userId = locals.user?.id;
  if (!userId) throw error(401, 'Unauthorized');
  
  const data = await request.json();
  const trip = await createTrip(userId, data);
  
  return json(trip, { status: 201 });
};
```

### Dynamic Routes
```typescript
// src/routes/api/trips/[id]/+server.ts
import type { RequestHandler } from './$types';

export const GET: RequestHandler = async ({ params, locals }) => {
  const trip = await getTrip(params.id, locals.user?.id);
  if (!trip) throw error(404, 'Trip not found');
  
  return json(trip);
};

export const PUT: RequestHandler = async ({ params, request, locals }) => {
  const data = await request.json();
  const trip = await updateTrip(params.id, locals.user?.id, data);
  
  return json(trip);
};

export const DELETE: RequestHandler = async ({ params, locals }) => {
  await deleteTrip(params.id, locals.user?.id);
  return json({ success: true });
};
```

## Common Patterns

### Form Handling with Progressive Enhancement
```svelte
<script lang="ts">
  import { enhance } from '$app/forms';
  import type { ActionData } from './$types';
  
  interface Props {
    form?: ActionData;
  }
  const { form }: Props = $props();
  
  let loading = $state(false);
</script>

<form 
  method="POST" 
  use:enhance={() => {
    loading = true;
    return async ({ update }) => {
      await update();
      loading = false;
    };
  }}
>
  <input name="email" type="email" required />
  <button disabled={loading}>
    {loading ? 'Submitting...' : 'Submit'}
  </button>
  
  {#if form?.error}
    <p class="error">{form.error}</p>
  {/if}
</form>
```

### Optimistic UI Updates
```typescript
let items = $state<Item[]>([]);

async function deleteItem(id: string) {
  // Optimistic update
  const original = items;
  items = items.filter(i => i.id !== id);
  
  try {
    await fetch(`/api/items/${id}`, { method: 'DELETE' });
  } catch (err) {
    // Rollback on error
    items = original;
    toast.error('Failed to delete');
  }
}
```

### Infinite Loading
```svelte
<script lang="ts">
  let items = $state<Item[]>([]);
  let page = $state(1);
  let hasMore = $state(true);
  let loading = $state(false);
  
  async function loadMore() {
    if (loading || !hasMore) return;
    
    loading = true;
    const response = await fetch(`/api/items?page=${page}`);
    const newItems = await response.json();
    
    items = [...items, ...newItems];
    hasMore = newItems.length > 0;
    page++;
    loading = false;
  }
  
  // Trigger on scroll
  $effect(() => {
    function handleScroll() {
      if (window.innerHeight + window.scrollY >= document.body.offsetHeight - 500) {
        loadMore();
      }
    }
    
    window.addEventListener('scroll', handleScroll);
    return () => window.removeEventListener('scroll', handleScroll);
  });
</script>
```

## Performance Optimization

### Lazy Loading
```svelte
<script lang="ts">
  import { browser } from '$app/environment';
  
  let HeavyComponent: any = $state(null);
  
  $effect(() => {
    if (browser && !HeavyComponent) {
      import('./HeavyComponent.svelte').then(module => {
        HeavyComponent = module.default;
      });
    }
  });
</script>

{#if HeavyComponent}
  <svelte:component this={HeavyComponent} />
{:else}
  <div>Loading...</div>
{/if}
```

### Debouncing Input
```svelte
<script lang="ts">
  let searchInput = $state('');
  let debouncedSearch = $state('');
  let timeout: ReturnType<typeof setTimeout> | null = null;
  
  $effect(() => {
    if (timeout) clearTimeout(timeout);
    
    timeout = setTimeout(() => {
      debouncedSearch = searchInput;
    }, 300);
    
    return () => {
      if (timeout) clearTimeout(timeout);
    };
  });
  
  // Use debouncedSearch for API calls
  let results = $derived.by(async () => {
    if (!debouncedSearch) return [];
    const res = await fetch(`/api/search?q=${debouncedSearch}`);
    return res.json();
  });
</script>
```

### Batch Updates
```typescript
let items = $state<Item[]>([]);

// ❌ Bad: Multiple updates
function addMultiple(newItems: Item[]) {
  newItems.forEach(item => {
    items = [...items, item]; // Triggers reactivity each time
  });
}

// ✅ Good: Single batch update
function addMultiple(newItems: Item[]) {
  items = [...items, ...newItems]; // Triggers once
}
```

## Type Safety

### Page Data Types
```typescript
// src/routes/dashboard/+page.server.ts
import type { PageServerLoad } from './$types';

export const load: PageServerLoad = async ({ locals }) => {
  return {
    trips: await getTrips(locals.user?.id),
    user: locals.user
  };
};

// src/routes/dashboard/+page.svelte
import type { PageData } from './$types';

interface Props {
  data: PageData; // Auto-generated from load function
}
const { data }: Props = $props();

// TypeScript knows data.trips and data.user exist
```

### Component Props Types
```typescript
// Button.svelte
interface Props {
  variant?: 'primary' | 'secondary';
  size?: 'sm' | 'md' | 'lg';
  disabled?: boolean;
  onclick?: () => void;
  children?: any;
}

const { 
  variant = 'primary',
  size = 'md',
  disabled = false,
  onclick,
  children
}: Props = $props();
```

## Common Pitfalls

### ❌ Mutating State Directly (Collections)
```typescript
// ❌ Wrong: Direct mutation doesn't trigger reactivity
let items = $state([1, 2, 3]);
items.push(4); // Won't update UI

// ✅ Right: Reassignment triggers reactivity
items = [...items, 4];
```

### ❌ Using $: Reactive Declarations
```typescript
// ❌ Wrong: Old Svelte 4 syntax
let count = $state(0);
$: doubled = count * 2;

// ✅ Right: Use $derived
let count = $state(0);
let doubled = $derived(count * 2);
```

### ❌ Forgetting Return Type on Load Functions
```typescript
// ❌ Wrong: No type safety
export async function load({ params }) {
  return { trip: await getTrip(params.id) };
}

// ✅ Right: Typed
export const load: PageServerLoad = async ({ params }) => {
  return { trip: await getTrip(params.id) };
};
```

## Quick Reference

### File Naming
- Pages: `+page.svelte`
- Layouts: `+layout.svelte`
- Server loads: `+page.server.ts` / `+layout.server.ts`
- Universal loads: `+page.ts` / `+layout.ts`
- API endpoints: `+server.ts`
- Error boundaries: `+error.svelte`

### Runes
- `$state()` - reactive variables
- `$derived()` - computed values
- `$effect()` - side effects
- `$props()` - component props
- `$bindable()` - two-way binding

### Navigation
- `goto('/path')` - programmatic navigation
- `<a href="/path">` - standard links
- `invalidate('/api/data')` - rerun load functions
- `invalidateAll()` - rerun all load functions

### Forms
- `use:enhance` - progressive enhancement
- Form actions in `+page.server.ts`
- `export const actions = { default, named }`

## Getting Help

Ask:
- "How do I use $state with arrays/objects?"
- "What's the difference between +page.ts and +page.server.ts?"
- "How do I handle forms in SvelteKit?"
- "How do I optimize this reactive code?"
- "Should this be $state or $derived?"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/br0ck25) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
