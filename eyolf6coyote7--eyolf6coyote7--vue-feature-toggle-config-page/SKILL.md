---
name: vue-feature-toggle-config-page
description: Build a Vue 3 admin page that lists feature toggles or remote config keys as cards with category badges, on/off switches, beta tags, and metadata footer. Use when this capability is needed.
metadata:
  author: Eyolf6Coyote7
---

## When to use

Trigger when the user asks to:
- add a feature flag / feature toggle / remote config admin page
- list configuration items with on/off controls
- mentions `toggles`, `FeatureTogglesPage`, `RemoteConfigPage`, `tc-cat`, `tc-beta`, `tc-actions`
- add a new toggle category (workflow / notifications / compliance / beta)

## Context

`admin-dashboard/src/views/FeatureTogglesPage.vue:1-167` is the canonical pattern. Each toggle card has:

1. **Name + category badge** — `nameKey` for i18n + `categoryKey` + `categoryColor` + `categoryBg` per category. Beta items also get a green `tc-beta` pill.
2. **Description line** — `descKey` (i18n) explaining what the toggle does.
3. **Metadata footer** — `Created: <date>` + `Last toggled: <date> by <user>` separated by a `meta-dot`.
4. **Switch control** — pill-shaped `.toggle` div with `.toggle.on` modifier; click handler flips `tog.enabled` directly on the local ref array. NO API call (this is demo UI).
5. **Off state** — `.toggle-card.off` class adds left border + grey background + faded text.

The categories use a color token system stored INSIDE the data array (not in CSS):
- workflow → `#1D4ED8` on `#DBEAFE`
- notifications → `#626468` on `#E1E2E7`
- compliance → `#93000A` on `#FFDAD6`
- beta → `#1D5200` on `#9BFA6B`

`RemoteConfigPage.vue` uses the SAME card shell but replaces the toggle with a value/edit input. Re-use the same CSS classes.

## Operating instructions

1. Copy `FeatureTogglesPage.vue` as the starting template.
2. Define a `toggles` ref array of objects: `{ nameKey, categoryKey, categoryColor, categoryBg, descKey, created, lastToggled, enabled, beta? }`.
3. Render each card via `v-for="tog in toggles"` with class binding `:class="['toggle-card', { off: !tog.enabled }]"`.
4. Toggle handler: `@click="tog.enabled = !tog.enabled"` directly on the ref. Wrap in `<DemoTooltip :message="$t('demo.toggleRequired')">` so reviewers see the demo gate.
5. Add an info banner at the top using `.info-banner` (left border `#0060A9`).
6. Add ALL i18n keys to BOTH `en.json` and `zh-TW.json` under the `toggles.*` namespace.
7. Add the route to `src/router/index.ts` (e.g. `/feature-flags`) and a sidebar entry.
8. For NEW categories not in the existing palette, add a new color pair and document it in this skill file's "category palette" section.

## Reusable prompts / code patterns

Toggle card data shape:
```ts
const toggles = ref([
  {
    nameKey: 'toggles.autoEscalation',
    categoryKey: 'toggles.workflow',
    categoryColor: '#1D4ED8',
    categoryBg: '#DBEAFE',
    descKey: 'toggles.autoEscalationDesc',
    created: 'Nov 1, 2024',
    lastToggled: 'Dec 15, 2024 by Li Wei',
    enabled: true,
  },
  // ...
])
```

Card template (preserve all class names so existing CSS resolves):
```vue
<div
  v-for="tog in toggles"
  :key="tog.nameKey"
  :class="['toggle-card', { off: !tog.enabled }]"
>
  <div class="tc-content">
    <div class="tc-top">
      <span class="tc-name">{{ $t(tog.nameKey) }}</span>
      <span class="tc-cat" :style="{ background: tog.categoryBg, color: tog.categoryColor }">
        {{ $t(tog.categoryKey) }}
      </span>
      <span v-if="tog.beta" class="tc-beta">{{ $t('toggles.beta') }}</span>
    </div>
    <p class="tc-desc">{{ $t(tog.descKey) }}</p>
    <div class="tc-meta">
      <span class="meta-item">Created: {{ tog.created }}</span>
      <span class="meta-dot"></span>
      <span class="meta-item">Last toggled: {{ tog.lastToggled }}</span>
    </div>
  </div>
  <div class="tc-actions">
    <DemoTooltip :message="$t('demo.toggleRequired')">
      <div :class="['toggle', { on: tog.enabled }]" @click="tog.enabled = !tog.enabled">
        <div class="toggle-thumb"></div>
      </div>
    </DemoTooltip>
  </div>
</div>
```

Info banner (always include at top of page):
```vue
<div class="info-banner">
  <span class="material-symbols-outlined" style="font-size: 20px; color: #0060a9">info</span>
  <span class="banner-text">{{ $t('toggles.warning') }}</span>
</div>
```

## Anti-patterns

- Do NOT call an API on toggle change — this is a demo page; flip the local ref only.
- Do NOT use `el-switch` — the project uses raw `.toggle` / `.toggle-thumb` divs for full visual control.
- Do NOT hardcode category colors at the call site; always store in the data array as `categoryColor` / `categoryBg`.
- Do NOT skip `<DemoTooltip>` on the switch — reviewers rely on it to identify intentionally inert UI.
- Do NOT introduce a separate `category` enum file; the categories live inline in the data array per the project's portfolio-grade simplicity rule.

## References

- `admin-dashboard/src/views/FeatureTogglesPage.vue:8-91` — full `toggles` array shape
- `admin-dashboard/src/views/FeatureTogglesPage.vue:120-166` — card template
- `admin-dashboard/src/views/FeatureTogglesPage.vue:347-372` — `.toggle` / `.toggle.on` CSS
- `admin-dashboard/src/views/RemoteConfigPage.vue` — sibling page using same shell with a value/edit control

---
> Source: [Eyolf6Coyote7/Eyolf6Coyote7](https://github.com/Eyolf6Coyote7/Eyolf6Coyote7) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-26 -->
