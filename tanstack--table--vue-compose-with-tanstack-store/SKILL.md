---
name: vuecompose-with-tanstack-store
description: > Use when this capability is needed.
metadata:
  author: TanStack
---

# Compose @tanstack/vue-table with @tanstack/vue-store

## Dependencies

```bash
pnpm add @tanstack/vue-table @tanstack/vue-store
```

`@tanstack/vue-store` provides `createAtom`, `useSelector`, and the `shallow` comparator. The
Vue table adapter is built on TanStack Store internally — this skill is about exposing that
machinery to your own app code via external atoms.

## Setup — own a slice, share it across components

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { createAtom, useSelector } from '@tanstack/vue-store'
import {
  FlexRender,
  createColumnHelper,
  createPaginatedRowModel,
  createSortedRowModel,
  rowPaginationFeature,
  rowSortingFeature,
  sortFns,
  tableFeatures,
  useTable,
  type PaginationState,
  type SortingState,
} from '@tanstack/vue-table'

type Person = { firstName: string; lastName: string; age: number }

const _features = tableFeatures({ rowSortingFeature, rowPaginationFeature })
const columnHelper = createColumnHelper<typeof _features, Person>()
const columns = columnHelper.columns([
  columnHelper.accessor('firstName', { header: 'First' }),
  columnHelper.accessor('age', { header: 'Age' }),
])

// 1) Module / setup scope — stable atom identity. createAtom returns the same atom every call;
//    do NOT recreate it inside a watcher or render fn.
const sortingAtom = createAtom<SortingState>([])
const paginationAtom = createAtom<PaginationState>({
  pageIndex: 0,
  pageSize: 10,
})

// 2) Reactive reads anywhere — sibling components can call useSelector on the same atom.
const sorting = useSelector(sortingAtom)
const pagination = useSelector(paginationAtom)

const data = ref<Person[]>([])

// 3) Pass via `options.atoms`. NO `onSortingChange` / `onPaginationChange` needed —
//    `table.setSorting()` writes through to your atom.
const table = useTable({
  _features,
  _rowModels: {
    sortedRowModel: createSortedRowModel(sortFns),
    paginatedRowModel: createPaginatedRowModel(),
  },
  columns,
  data,
  atoms: {
    sorting: sortingAtom,
    pagination: paginationAtom,
  },
})
</script>

<template>
  <div>
    Page {{ pagination.pageIndex + 1 }}, sorted by
    {{ sorting[0]?.id ?? 'none' }}
  </div>
</template>
```

Source: `examples/vue/basic-external-atoms/src/App.tsx`.

## Core Patterns

### 1. The three read surfaces — pick the narrowest

```ts
// (a) Atom — narrowest. Reads/writes one slice.
table.atoms.sorting.get()
table.atoms.sorting.set([{ id: 'age', desc: true }])
useSelector(table.atoms.sorting) // reactive ref-like
computed(() => table.atoms.sorting.get()) // alternative

// (b) Flat store — full snapshot.
table.store.state // readonly
table.store.state.sorting // current value

// (c) useTable selector — typed reactive projection.
const table = useTable(opts, (s) => ({ sorting: s.sorting }))
table.state.sorting
```

`table.atoms.<slice>` only contains slices for features registered in `_features`. If
`rowSortingFeature` is not registered, `table.atoms.sorting` is `undefined`.

### 2. Atom precedence rules

Per slice, the resolution order is:

```
options.atoms[slice]    >  options.state[slice]  >  table.baseAtoms[slice]
(external, you own)        (controlled state)       (internal default)
```

```ts
// ❌ Passing both for the SAME slice. `atoms.sorting` wins; `state.sorting` is silently dead.
useTable({
  _features,
  _rowModels: {},
  columns,
  data,
  state: {
    get sorting() {
      return localSorting.value
    },
  }, // ignored
  onSortingChange: setLocalSorting, // ignored
  atoms: { sorting: sortingAtom }, // wins
})
```

Pick **one mechanism per slice**. Different slices can use different mechanisms freely.

### 3. When to choose external atoms vs `state` + `on[State]Change`

External atoms are better when:

- A sibling component (sidebar, header, breadcrumbs) needs to read the same slice without going
  through `table`.
- You want to persist the slice (localStorage, URL params) — write a `watch(atom, persist)`
  outside the table.
- You're integrating with TanStack Query and want `queryKey: ['x', useSelector(atom).value]`
  to drive refetches without `on*Change` plumbing.

`state` + `on[State]Change` (with **getter wrappers** on each slice — Vue-specific) is fine
when:

- You're migrating from v8 and want the smallest diff.
- The slice is already a `ref` you don't want to convert.

### 4. Reset is YOUR responsibility for owned atoms

```ts
// ❌ table.reset() does NOT clear external atoms — it only resets internally-owned slices.
table.reset()

// ✅ Reset your atoms yourself.
sortingAtom.set([])
paginationAtom.set({ pageIndex: 0, pageSize: 10 })
table.reset() // optional — for any slices the table owns internally
```

Same applies to per-slice resets: `table.resetSorting()` will update through the atom because
the atom is the slice's source of truth — but a base-atom reset (`table.resetSorting(true)`)
only resets internal default state, not your owned atom. Read the source if you're chaining
resets across mixed ownership.

### 5. Persisting an atom outside the table

```ts
import { watch } from 'vue'

const sortingAtom = createAtom<SortingState>(
  JSON.parse(localStorage.getItem('mySort') ?? '[]'),
)
const sorting = useSelector(sortingAtom)
watch(sorting, (s) => localStorage.setItem('mySort', JSON.stringify(s)))
```

The table now writes through `table.setSorting()` → `sortingAtom.set(...)` → the watcher
persists. No `on*Change` involved.

## Common Mistakes

### Creating atoms inside a render fn or component body without stable identity (CRITICAL)

```vue
<script setup>
// ❌ A new atom every component setup → the table re-binds every mount → "state resets"
//    feels random to the user. Only an issue when atoms are created in a reactive scope.
const sortingAtom = computed(() => createAtom < SortingState > [])
</script>
```

```vue
<script setup>
// ✅ Setup scope is once per component instance — fine.
const sortingAtom = createAtom < SortingState > []

// ✅ Module scope — shared across instances.
// (declare at top of file outside <script setup>)
</script>
```

### Passing the same slice via both `state` and `atoms` (HIGH)

`atoms` wins; the `state` plumbing is silently dead. Pick one mechanism per slice.

### Pairing `atoms.sorting` with `onSortingChange` (MEDIUM)

```ts
// ❌ Confusing — the handler never fires usefully because the atom is the writeback.
useTable({
  // ...
  atoms: { sorting: sortingAtom },
  onSortingChange: (u) => {
    /* dead */
  },
})
```

When using `atoms` for a slice, drop the matching `on[State]Change`. Subscribe to the atom
itself (`watch(useSelector(sortingAtom), ...)` or `subscribe`) if you need side effects on change.

### Expecting `table.reset()` to clear external atoms (HIGH)

```ts
// ❌
table.reset() // does not touch your atoms

// ✅ Reset what you own.
sortingAtom.set([])
paginationAtom.set({ pageIndex: 0, pageSize: 10 })
```

### Reading `table.state.<slice>` in a deeply-nested component when only that slice matters (MEDIUM)

```ts
// ❌ All of table.state is computed and tracked even if the component only cares about rowSelection.
const table = inject(TABLE)
const count = computed(() => Object.keys(table.state.rowSelection).length)

// ✅ Subscribe to the atom directly.
const selection = useSelector(table.atoms.rowSelection)
const count = computed(() => Object.keys(selection.value).length)
```

### Reading `atom.get()` outside a reactive scope (HIGH — Vue-specific)

```ts
// ❌ One-shot read; doesn't track.
const sorting = sortingAtom.get()

// ✅ useSelector returns a reactive ref-like.
const sorting = useSelector(sortingAtom)

// ✅ or wrap in computed.
const sorting = computed(() => sortingAtom.get())
```

### Hallucinating pre-v9 API names (CRITICAL)

`useVueTable`, `table.getState()` — both v8. v9 uses `useTable` and `table.store.state` /
`table.state` / `table.atoms.<slice>.get()`. See `tanstack-table/vue/migrate-v8-to-v9`.

### "API missing" because feature not in `_features` (CRITICAL — v9-specific)

`table.atoms.sorting` is `undefined` unless `rowSortingFeature` is registered. The fix isn't to
fall back to `state` — it's to add the feature to `tableFeatures({...})`.

## See Also

- `tanstack-table/vue/table-state` — read surfaces and selector reactivity
- `tanstack-table/vue/client-to-server` — atoms + manual modes wiring
- `tanstack-table/vue/compose-with-tanstack-query` — atoms in the queryKey
- `tanstack-table/vue/production-readiness` — `useSelector` per-slice for narrow re-renders

---
> Source: [TanStack/table](https://github.com/TanStack/table) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-24 -->
