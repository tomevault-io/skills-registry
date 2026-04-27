---
name: vue-echarts-kpi-dashboard
description: Build a Vue 3 KPI dashboard page with 4-column metric cards plus 4 echarts panels (bar, line area, doughnut pie, custom bar list) using vue-echarts and tree-shaken echarts core. Use when this capability is needed.
metadata:
  author: Eyolf6Coyote7
---

## When to use

Trigger when the user asks to:
- add a "dashboard / overview / analytics / metrics" page
- show KPI cards + charts (bar / line / pie / doughnut)
- mentions `vue-echarts`, `VChart`, `barOption`, `lineOption`, `pieOption`, `KpiData`, `kpiCards`
- visualize data from `api.getKpi()` or any endpoint returning `{ approvalRate, monthlyVolume, avgProcessingTime }`-like shape

DO NOT use this skill for simple stat strips with no charts — use `vue-filterable-data-table` (the stats-row block) for that.

## Context

`admin-dashboard/src/views/DashboardPage.vue:1-289` is the canonical KPI dashboard. Key conventions:

1. **Tree-shake echarts**: import individual `PieChart`, `BarChart`, `LineChart` from `echarts/charts` and components from `echarts/components`, then `use([...])` once at module top (lines 1-26). Do NOT import the full echarts bundle.
2. **`<v-chart>` per panel**: use `vue-echarts` `<v-chart :option="..." style="height: 300px" autoresize />`. Always pass a `computed` option, never a ref of literal — the option must re-evaluate when data arrives.
3. **KPI data shape** (`KpiData`):
   ```ts
   { approvalRate: { approved: number; rejected: number; pending: number }
     monthlyVolume: { month: string; count: number }[]
     avgProcessingTime: { month: string; hours: number }[] }
   ```
4. **KPI card model**: `{ label, value, trend, trendColor, borderColor }` driving the 4-column grid with `border-left: 4px solid` color tokens (`#409EFF`, `#E6A23C`, `#67C23A`, `#F56C6C`).
5. **Doughnut center text**: use `graphic: [{ type: 'text', ... }]` array in pie option (lines 167-190) to render the total + label inside the ring.
6. **Brand colors**: `#0060A9` primary dark, `#409EFF` primary, `#67C23A` success, `#E6A23C` warning, `#F56C6C` danger, `#A0CFFF` light blue.

## Operating instructions

1. Copy the `<script setup>` head from `DashboardPage.vue` (lines 1-32) verbatim — including `use([...])` registration — and only add new chart types if needed.
2. Define a `kpi` ref typed `KpiData | null`. Load via `api.getKpi()` in `onMounted`.
3. Wrap every chart option in `computed(() => kpi.value ? {...} : {})` so the chart paints when data arrives.
4. Use the `kpiCards` `computed` array pattern (lines 34-70) for KPI cards — do NOT inline cards in template.
5. Use `<v-chart>` with `style="height: 300px"` and `autoresize` on every chart panel.
6. For grids: 4 KPI cards top, then `.charts-row` with 2 columns repeating. Wire `v-if="kpi"` so charts don't render before data lands.
7. Add `t('dashboard.xxx')` keys for every label (axis legends, card labels) in `i18n/en.json` and `i18n/zh-TW.json`.

## Reusable prompts / code patterns

Echarts registration (top of file):
```ts
import VChart from 'vue-echarts'
import { use } from 'echarts/core'
import { PieChart, BarChart, LineChart } from 'echarts/charts'
import { TitleComponent, TooltipComponent, LegendComponent, GridComponent } from 'echarts/components'
import { CanvasRenderer } from 'echarts/renderers'

use([PieChart, BarChart, LineChart, TitleComponent, TooltipComponent, LegendComponent, GridComponent, CanvasRenderer])
```

KPI cards `computed`:
```ts
const kpiCards = computed(() => {
  if (!kpi.value) return []
  return [
    { label: t('dashboard.avgTurnaround'), value: '6.2 hours', trend: '↓ 12% vs last month', trendColor: '#67C23A', borderColor: '#409EFF' },
    // ...
  ]
})
```

Bar option pattern (compact, horizontal-paired bars):
```ts
const barOption = computed(() => ({
  tooltip: { trigger: 'axis' },
  legend: { right: 0, top: 0, data: [t('dashboard.submitted'), t('dashboard.completed')], textStyle: { fontSize: 12, color: '#404752' } },
  grid: { left: 40, right: 16, bottom: 40, top: 24 },
  xAxis: { type: 'category', data: kpi.value!.monthlyVolume.map((m) => m.month), axisLabel: { fontSize: 10, color: '#404752' } },
  yAxis: { type: 'value', axisLabel: { fontSize: 10, color: '#404752' }, splitLine: { lineStyle: { color: 'rgba(192,199,212,0.1)' } } },
  series: [
    { type: 'bar', name: t('dashboard.submitted'), data: kpi.value!.monthlyVolume.map((m) => m.count), itemStyle: { color: '#409EFF' }, barWidth: 32 },
  ],
}))
```

Doughnut center text via `graphic`:
```ts
graphic: [
  { type: 'text', left: 'center', top: '38%', style: { text: String(total), font: '700 36px Inter', fill: '#191C1E', textAlign: 'center' } },
  { type: 'text', left: 'center', top: '50%', style: { text: t('dashboard.total'), font: '400 14px Inter', fill: '#707784', textAlign: 'center' } },
],
```

## Anti-patterns

- Do NOT `import echarts from 'echarts'` (full bundle) — always tree-shake via `echarts/core` + per-chart imports.
- Do NOT use `ref` for chart options — always `computed` so recompute happens when `kpi.value` arrives.
- Do NOT hardcode chart heights in CSS — use the inline `style="height: 300px"` so SSR / hydration paints correctly.
- Do NOT skip `autoresize` on `<v-chart>` — without it, sidebar collapse breaks layout.
- Do NOT use `el-card` wrappers — the project uses raw `.chart-card` div with the project's box-shadow tokens.

## References

- `admin-dashboard/src/views/DashboardPage.vue:1-26` — echarts tree-shake registration
- `admin-dashboard/src/views/DashboardPage.vue:34-70` — `kpiCards` computed
- `admin-dashboard/src/views/DashboardPage.vue:72-110` — `barOption` pattern
- `admin-dashboard/src/views/DashboardPage.vue:112-158` — `lineOption` with `markLine` and gradient `areaStyle`
- `admin-dashboard/src/views/DashboardPage.vue:160-211` — `pieOption` with center `graphic` text
- `admin-dashboard/src/api/mock-client.ts:28-32` — `KpiData` type definition

---
> Source: [Eyolf6Coyote7/Eyolf6Coyote7](https://github.com/Eyolf6Coyote7/Eyolf6Coyote7) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-26 -->
