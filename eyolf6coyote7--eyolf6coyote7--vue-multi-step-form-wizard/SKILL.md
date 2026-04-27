---
name: vue-multi-step-form-wizard
description: Build a 3-step Vue 3 wizard (template picker → form → review/submit) matching the NewRequestPage pattern with stepper UI, per-step validation, and i18n keys. Use when this capability is needed.
metadata:
  author: Eyolf6Coyote7
---

## When to use

Trigger when the user asks to:
- add a new "create / new request / submit" page in `employee-portal`
- build any multi-step form with template picker, fillable fields, and a final review screen
- mentions `currentStep`, `nextStep`, `prevStep`, `stepper`, `template-grid`, `step-circle`
- adds a new request type (leave / purchase / travel / equipment / expense)

DO NOT use this skill for simple single-form pages — use `vue-filterable-data-table` for table-centric pages or write a plain `<form>` instead.

## Context

`employee-portal` standardises new-request UX as a 3-step wizard rendered inside one Vue SFC. The reference implementation is `employee-portal/src/views/NewRequestPage.vue:1-352`. Steps:

1. **Template selection** — `templates` array of `{ id, nameKey, descKey, icon, iconBg, iconColor }` rendered as a 3-column grid; click selects, `selectedTemplate` ref tracks state.
2. **Form fields** — `form` ref holds `{ title, department, amount, priority, description, justification, attachment }`; required-field guard happens in `nextStep()`.
3. **Review & submit** — read-only key/value rows, `priority` rendered as colored pill, `submitRequest()` calls `ElMessage.success` and `router.push('/dashboard')`.

The stepper visual (circle + connector + label) at lines 119-143 is shared visual language with `progress-stepper` patterns elsewhere — preserve the `step-circle` / `step-connector.filled` / `step-connector.half` class names so existing CSS applies.

`DemoTooltip` wraps any non-functional CTA (upload zone line 273, submit button line 342) — every disabled-in-demo control MUST be wrapped this way to keep the portfolio's demo gating consistent.

## Operating instructions

1. Open `employee-portal/src/views/NewRequestPage.vue` and copy its `<script setup>` skeleton verbatim — keep imports for `ref`, `computed`, `useRouter`, `ElMessage`, `useI18n`, `DemoTooltip`.
2. Define a `templates` array of `{ id, nameKey, descKey, icon, iconBg, iconColor }` matching the page's color palette: `#EFF6FF/#2563EB`, `#F0FDF4/#16A34A`, `#FFF7ED/#EA580C`, `#FAF5FF/#9333EA`, `#F0FDFA/#0D9488`, `#F1F5F9/#475569`.
3. Define a `form` ref with the fields the new wizard needs. Always include `priority` as one of `low | medium | high | urgent` so the priority pill CSS at lines 855-880 works.
4. `nextStep()` MUST guard required fields with `ElMessage.warning(...)` BEFORE incrementing `currentStep.value`.
5. Add new translation keys to BOTH `employee-portal/src/i18n/en.json` and `employee-portal/src/i18n/zh-TW.json` under a top-level namespace matching the wizard (e.g. `newRequest.*`, `priority.*`, `departments.*`).
6. Wrap every demo-disabled CTA in `<DemoTooltip :message="t('demo.xxxRequired')">`.
7. Add a Storybook story under `employee-portal/src/views/<Name>.stories.ts` mirroring `NewRequestPage.stories.ts`.

## Reusable prompts / code patterns

Stepper template block (copy verbatim, change `stepLabels` source only):
```vue
<div class="stepper">
  <div
    v-for="(label, i) in stepLabels"
    :key="i"
    class="step"
    :class="{ completed: i + 1 < currentStep, active: i + 1 === currentStep }"
  >
    <div class="step-circle">
      <span v-if="i + 1 < currentStep" class="material-symbols-outlined" style="font-size: 14px; color: #fff">check</span>
      <span v-else>{{ i + 1 }}</span>
    </div>
    <span class="step-label">{{ label }}</span>
    <div
      v-if="i < stepLabels.length - 1"
      class="step-connector"
      :class="{ filled: i + 1 < currentStep, half: i + 1 === currentStep }"
    ></div>
  </div>
</div>
```

Step navigation guards:
```ts
function nextStep() {
  if (currentStep.value === 1 && !selectedTemplate.value) {
    ElMessage.warning('Please select a template')
    return
  }
  if (currentStep.value === 2 && !form.value.title) {
    ElMessage.warning('Please enter a request title')
    return
  }
  currentStep.value++
}
```

Priority radio block + pill (keep priorityKeys map identical so existing CSS resolves):
```ts
const priorityKeys: Record<string, string> = {
  low: 'priority.low',
  medium: 'priority.medium',
  high: 'priority.high',
  urgent: 'priority.urgent',
}
```

## Anti-patterns

- Do NOT introduce a router-based wizard (one route per step). Stay single-SFC with `currentStep` ref — the design depends on shared form state without round-trips.
- Do NOT replace `ElMessage.warning` with custom toasts; the project standardises on Element Plus messages.
- Do NOT wire a real API call in `submitRequest()` — the demo build uses mocked submission. Connect via `@/api` only when `VITE_MOCK !== 'true'`.
- Do NOT inline strings in templates — every visible label MUST go through `t('namespace.key')`.
- Do NOT skip `DemoTooltip` on demo-disabled controls; reviewers rely on it to identify intentionally inert UI.

## References

- `employee-portal/src/views/NewRequestPage.vue:14-63` — `templates` array shape
- `employee-portal/src/views/NewRequestPage.vue:66-101` — `form` ref + `nextStep`/`prevStep`/`submitRequest`
- `employee-portal/src/views/NewRequestPage.vue:119-143` — stepper template
- `employee-portal/src/views/NewRequestPage.vue:855-880` — `.review-priority` color tokens
- `employee-portal/src/components/DemoTooltip.vue` — demo gating wrapper

---
> Source: [Eyolf6Coyote7/Eyolf6Coyote7](https://github.com/Eyolf6Coyote7/Eyolf6Coyote7) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-26 -->
