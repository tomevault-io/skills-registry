---
name: vue-filterable-data-table
description: Build a Vue 3 filterable list page with stat chips, multi-select filter bar, search input, paginated table, and row click navigation — matching UserManagementPage/RequestListPage. Use when this capability is needed.
metadata:
  author: Eyolf6Coyote7
---

## When to use

Trigger when the user asks to:
- add a new "list / management / queue / history" page in `admin-dashboard` or `employee-portal`
- show records with filter dropdowns + search + table + pagination
- mentions `filtered`, `roleFilter`, `statusFilter`, `typeFilter`, `data-table`, `pg-btn`, `stats-row`, `filter-bar`
- replace a placeholder list with a real API-backed table

DO NOT use this skill for simple key/value detail pages or wizards — use `vue-multi-step-form-wizard` for forms.

## Context

The project has TWO canonical filterable-table pages, both built on the SAME pattern:

- `admin-dashboard/src/views/UserManagementPage.vue:1-228` — users list with `roleFilter` + search
- `employee-portal/src/views/RequestListPage.vue:1-211` — requests list with `statusFilter` + `typeFilter` + search

Both share:
1. `ref` for raw `data`, `loading`, `search`, and one filter ref per dropdown
2. `onMounted` calls `api.getXxx()` from `@/api` (resolves mock or real client via `VITE_MOCK`)
3. A `watch([data, search, ...filters], ...)` block that recomputes a `filtered` ref — note RequestListPage uses `watch+ref` not `computed` (line 22-38) to keep parity with UserManagementPage's pattern
4. Filter bar above the table with `select.filter-select` controls and a `search` input wrapped in `.filter-search` / `.filter-input-wrap`
5. Pagination footer with `.pg-btn` / `.pg-btn.active` styling — currently static markup, the pattern reserves space for real pagination later

Status pills use a `statusStyle()` map function returning `{ bg, color, labelKey }` per status key (RequestListPage line 40-50). Replicate this pattern for any new column with categorical states.

## Operating instructions

1. Copy the `<script setup>` skeleton from `RequestListPage.vue` (cleaner than UserManagement because it has multi-filter):
   - Imports: `ref`, `onMounted`, `watch`, `useRouter`, `useI18n`, `api`, `DemoTooltip`
   - Type from `@/api` (define new type in `mock-client.ts` first if needed)
2. Declare ONE filter `ref` per filter dropdown, default `'all'`. Declare `search = ref('')`.
3. Use `watch([rawData, search, ...filters], () => { ... }, { immediate: true })` to populate `filtered.value` — do NOT use `computed` for this; the watch pattern matches the codebase.
4. Filter logic: lowercase the search term once, then `.filter()` with `(matchSearch && matchStatus && matchType)` — short-circuit on `'all'` for filter selects.
5. For status/role columns, write a `statusStyle(s) → { bg, color, labelKey }` helper and apply via `:style="{ background: ..., color: ... }"`.
6. Click rows with `@click="router.push('/things/' + row.id)"` if a detail page exists.
7. Wrap any non-functional action button (export, invite, more-actions) in `<DemoTooltip>`.
8. Add the route to `src/router/index.ts` and navigation entry to the sidebar component.
9. Mirror the test pattern from existing `*.stories.ts` (employee-portal only) — admin-dashboard does not use Storybook.
10. Add an api method to BOTH `mock-client.ts` and `real-client.ts` returning the same shape.

## Reusable prompts / code patterns

Filter watcher (do not replace with `computed`):
```ts
const filtered = ref<Row[]>([])
watch(
  [rows, search, statusFilter, typeFilter],
  () => {
    const q = search.value.toLowerCase()
    filtered.value = rows.value.filter((r) => {
      const matchSearch =
        r.title.toLowerCase().includes(q) || r.id.toLowerCase().includes(q)
      const matchStatus = statusFilter.value === 'all' || r.status === statusFilter.value
      const matchType = typeFilter.value === 'all' || r.type === typeFilter.value
      return matchSearch && matchStatus && matchType
    })
  },
  { immediate: true },
)
```

Stat chip row (left-border-colored):
```vue
<div class="stats-row">
  <div v-for="s in stats" :key="s.labelKey" class="stat-chip" :style="{ borderLeftColor: s.color }">
    <span class="stat-label">{{ $t(s.labelKey) }}</span>
    <span class="stat-value" :style="{ color: s.color }">{{ s.value }}</span>
  </div>
</div>
```

Status pill style helper:
```ts
function statusStyle(status: string) {
  const map: Record<string, { bg: string; color: string; labelKey: string }> = {
    pending:  { bg: '#FDF6EC', color: '#E6A23C', labelKey: 'status.pending' },
    approved: { bg: '#F0F9EB', color: '#67C23A', labelKey: 'status.approved' },
    rejected: { bg: '#FEF0F0', color: '#F56C6C', labelKey: 'status.rejected' },
  }
  return map[status] ?? { bg: '#F0F9EB', color: '#67C23A', labelKey: '' }
}
```

## Anti-patterns

- Do NOT use Element Plus `<el-table>` — the project uses raw `<table class="data-table">` for full styling control.
- Do NOT replace `watch + ref` with `computed` for `filtered` — it breaks parity with sibling pages.
- Do NOT call the API inside the watcher; load once in `onMounted`, filter client-side.
- Do NOT hardcode role/status colors at the call site — always go through the `statusStyle()` map.
- Do NOT use `<router-link>` for full-row clicks; use `@click="router.push(...)"` so the entire `<tr>` is the hit target.

## References

- `admin-dashboard/src/views/UserManagementPage.vue:22-39` — onMounted load + watch filter
- `admin-dashboard/src/views/UserManagementPage.vue:67-101` — stats row + filter bar markup
- `employee-portal/src/views/RequestListPage.vue:22-38` — multi-filter watch pattern
- `employee-portal/src/views/RequestListPage.vue:40-50` — `statusStyle` helper
- `admin-dashboard/src/api/index.ts` — VITE_MOCK client switching

---
> Source: [Eyolf6Coyote7/Eyolf6Coyote7](https://github.com/Eyolf6Coyote7/Eyolf6Coyote7) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-26 -->
