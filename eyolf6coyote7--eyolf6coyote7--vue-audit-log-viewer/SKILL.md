---
name: vue-audit-log-viewer
description: Build a Vue 3 audit-log table page with multi-field filter bar (event type / user / date range / severity), live indicator, monospace timestamps, severity-tinted rows, and large pagination. Use when this capability is needed.
metadata:
  author: Eyolf6Coyote7
---

## When to use

Trigger when the user asks to:
- add an audit log / activity log / event history page
- list timestamped events with filterable severity
- mentions `AuditEntry`, `eventColor`, `severity`, `formatTs`, `cell-mono`, `live-dot`, `row-warning`
- show a "Recent Activity" table with live updates

## Context

`admin-dashboard/src/views/AuditLogPage.vue:1-162` is the canonical implementation. Distinguishing characteristics vs `vue-filterable-data-table`:

1. **Multi-field filter bar** with grouped labels — each filter has `.fg-label` ABOVE the control + `.fg-search` for input fields.
2. **Live indicator** — `.th-live` with pulsing `.live-dot` (`#67C23A`) showing the table updates in real time.
3. **Monospace timestamps + IPs** — `.cell-mono` uses `JetBrains Mono` font; format dates via `formatTs(iso: string)` returning `Mar 26, 2026 14:32` style.
4. **Event color helper** — `eventColor(action)` returns `{ dot, label, bg, color }` keyed on action substrings (`Approved`/`Created` → green/blue, `Failed`/`Deleted` → red/orange, default → blue).
5. **Severity tag** — same severity-color helper, rendered as a small pill in the last column.
6. **Row tinting** — `<tr :class="{ 'row-warning': e.action.includes('Failed') }">` paints failed rows with `#fffbf0` background.
7. **Large pagination** — fake count "1-15 of 2,847 events" + 50/page selector — the project shows scale even in demo mode.

## Operating instructions

1. Copy the `<script setup>` and `<template>` from `AuditLogPage.vue` as the starting point.
2. Define `entries` ref typed `AuditEntry[]` and load via `api.getAuditLog()` in `onMounted`.
3. Define `eventFilter` and `severityFilter` refs (default `'all'`) — filter logic is intentionally minimal in the canonical page (line 19: `const filtered = computed(() => entries.value)`); add real filter logic only if the user asks for it.
4. Implement `formatTs(iso)` returning `<localeDateString> <localeTimeString hour12: false>`.
5. Implement `eventColor(action)` mapping action substrings to color tokens. Use this for the dot, severity tag bg, and severity tag color.
6. Wrap the export button in `<DemoTooltip :message="$t('demo.exportRequired')">`.
7. Render the live indicator at the top of the table card with `.th-live` + `.live-dot`.
8. Add ALL labels to `audit.*` namespace in BOTH `en.json` and `zh-TW.json`.
9. Use the existing pagination block — do NOT add real pagination unless explicitly asked.

## Reusable prompts / code patterns

`eventColor` helper:
```ts
function eventColor(action: string) {
  if (action.includes('Approved') || action.includes('Created'))
    return { dot: '#67C23A', label: action, bg: 'rgba(0,96,169,0.1)', color: '#0060A9' }
  if (action.includes('Failed') || action.includes('Deleted'))
    return { dot: '#BA1A1A', label: action, bg: 'rgba(225,133,0,0.2)', color: '#E18500' }
  return { dot: '#0060A9', label: action, bg: 'rgba(0,96,169,0.1)', color: '#0060A9' }
}
```

Timestamp formatter:
```ts
function formatTs(iso: string) {
  const d = new Date(iso)
  return (
    d.toLocaleDateString('en-US', { month: 'short', day: '2-digit', year: 'numeric' }) +
    ' ' +
    d.toLocaleTimeString('en-US', { hour: '2-digit', minute: '2-digit', hour12: false })
  )
}
```

Live indicator block:
```vue
<div class="table-header-row">
  <span class="th-title">{{ $t('audit.recentActivity') }}</span>
  <span class="th-live"><span class="live-dot"></span> {{ $t('audit.live') }}</span>
</div>
```

Row binding with severity tinting:
```vue
<tr v-for="e in filtered" :key="e.id" :class="{ 'row-warning': e.action.includes('Failed') }">
  <td class="cell-mono">{{ formatTs(e.timestamp) }}</td>
  <td>
    <div class="event-cell">
      <span class="event-dot" :style="{ background: eventColor(e.action).dot }"></span>
      <span class="event-text">{{ e.action }}</span>
    </div>
  </td>
  <!-- ... -->
  <td>
    <span class="severity-tag" :style="{ background: eventColor(e.action).bg, color: eventColor(e.action).color }">
      {{ e.action.includes('Failed') ? $t('audit.warning') : $t('audit.info') }}
    </span>
  </td>
</tr>
```

## Anti-patterns

- Do NOT replace `JetBrains Mono` with system mono — the design tokens depend on it.
- Do NOT compute severity at the row level via inline ternaries — always go through `eventColor()`.
- Do NOT add real-time WebSocket / polling for the live dot — it's a visual indicator, not a behavior.
- Do NOT collapse the per-filter `.fg-label` above each control; the grouped label/control pattern is the design contract.
- Do NOT show real pagination — keep the static "1-15 of 2,847" footer to demonstrate scale.

## References

- `admin-dashboard/src/views/AuditLogPage.vue:21-36` — `formatTs` + `eventColor` helpers
- `admin-dashboard/src/views/AuditLogPage.vue:55-90` — multi-field filter bar markup
- `admin-dashboard/src/views/AuditLogPage.vue:94-141` — table header with live indicator + row body
- `admin-dashboard/src/views/AuditLogPage.vue:344-345` — `.row-warning` tinting
- `admin-dashboard/src/api/mock-client.ts:19-26,160-178` — `AuditEntry` type + mock generator

---
> Source: [Eyolf6Coyote7/Eyolf6Coyote7](https://github.com/Eyolf6Coyote7/Eyolf6Coyote7) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-26 -->
