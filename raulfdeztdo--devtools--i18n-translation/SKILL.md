---
name: i18n-translation
description: > Use when this capability is needed.
metadata:
  author: raulfdeztdo
---

# i18n-translation: Vue 3 Internationalization for devtools

Implement complete internationalization (i18n) for this Vue 3 devtools project using `vue-i18n`. This skill provides a systematic workflow to eliminate all hardcoded strings and establish a scalable translation system.

## Project Context

This is a Vue 3 + Vite + Tailwind CSS SPA with multiple developer tools. The UI is bilingual (Spanish and English) with full i18n coverage. The project uses:

- **Framework:** Vue 3 with Composition API (`<script setup>`)
- **i18n library:** `vue-i18n` (installed and configured)
- **Router:** `vue-router` (already installed)
- **Build tool:** Vite
- **Languages:** Spanish (es) and English (en) — both fully translated (~460 keys each)

### Project Structure

```
src/
├── App.vue                          # Navigation shell (Composition API)
├── main.js                          # App entry + router + i18n setup
├── style.css                        # Tailwind + custom component classes
├── i18n/
│   ├── index.js                     # i18n config with locale detection
│   └── locales/
│       ├── es.json                  # Spanish translations (~460 keys)
│       └── en.json                  # English translations (~460 keys)
├── components/
│   └── LineNumberedTextarea.vue     # Shared component (Composition API)
└── views/
    ├── Home.vue                     # Tool grid (Composition API)
    ├── JSONLint.vue                 # Composition API
    ├── JSONSchemaValidator.vue      # Composition API
    ├── JSONCompare.vue              # Composition API
    ├── UUIDGenerator.vue            # Composition API
    ├── PasswordGenerator.vue        # Composition API
    ├── TimestampConverter.vue       # Composition API
    ├── Base64Converter.vue          # Composition API
    ├── URLConverter.vue             # Composition API
    ├── ColorPaletteGenerator.vue    # Composition API
    └── PHPSerializer.vue            # Composition API
```

**NOTE:** All views use Composition API with `<script setup>` and `useI18n()` / `t()`. The i18n migration is complete.

---

## Scope Rules

### IN SCOPE - Source Code i18n

- All `.vue` files in `src/views/` and `src/components/`
- `src/App.vue` (navigation, dropdown, mobile menu)
- Translation JSON files in `src/i18n/locales/`
- i18n plugin setup in `src/i18n/`

### OUT OF SCOPE

- `README.md`, `docs/`, any `.md` files
- `Dockerfile`, `docker-compose.yml`, `nginx.conf`
- Config files (`vite.config.js`, `tailwind.config.js`, `postcss.config.js`)

### Detection - Check Source Code Only

```bash
# Correct: check source code
Grep: "vue-i18n|useI18n|\\$t\\(" in src/
Glob: "src/**/locales/**/*.json"
Glob: "src/**/i18n/**"

# Wrong: don't check documentation
# Do NOT search in: README.md, docs/
```

---

## Implementation Workflow

### Phase 1: Install and Configure vue-i18n

1. **Install dependency:**
   ```bash
   npm install vue-i18n
   ```

2. **Create i18n configuration** (`src/i18n/index.js`):
   ```javascript
   import { createI18n } from 'vue-i18n'
   import es from './locales/es.json'
   import en from './locales/en.json'

   const i18n = createI18n({
     locale: localStorage.getItem('locale') || 'es',
     fallbackLocale: 'es',
     messages: { es, en },
     globalInjection: true
   })

   export default i18n
   ```

3. **Register in main.js:**
   ```javascript
   import i18n from './i18n'
   // ...
   app.use(i18n)
   app.use(router)
   app.mount('#app')
   ```

### Phase 2: Extract All Strings

Process each file systematically. This project has ~200-300 user-facing strings.

#### Namespace Structure

```
src/i18n/locales/
├── es.json    # Spanish (base language)
└── en.json    # English
```

#### Recommended Key Organization

```json
{
  "nav": {
    "home": "Inicio",
    "tools": "Herramientas",
    "lightMode": "Cambiar a modo claro",
    "darkMode": "Cambiar a modo oscuro"
  },
  "home": {
    "title": "Herramientas para desarrolladores",
    "subtitle": "{count} utilidades esenciales para tu flujo de trabajo diario..."
  },
  "tools": {
    "jsonlint": {
      "name": "JSONLint",
      "description": "Valida y formatea JSON",
      "tags": ["Validar", "Formatear"]
    }
  },
  "common": {
    "copy": "Copiar",
    "clear": "Limpiar",
    "validate": "Validar",
    "format": "Formatear",
    "generate": "Generar",
    "download": "Descargar",
    "copied": "Copiado al portapapeles",
    "error": "Error",
    "success": "Correcto"
  }
}
```

#### String Categories to Extract

| Category | Example | Key Pattern |
|----------|---------|-------------|
| Navigation | "Inicio", "Herramientas" | `nav.*` |
| Page titles | "JSONLint", "UUID Generator" | `tools.{tool}.name` |
| Descriptions | "Valida y formatea JSON" | `tools.{tool}.description` |
| Buttons | "Validar", "Copiar", "Limpiar" | `common.*` or `tools.{tool}.*` |
| Placeholders | "Introduce JSON aquí..." | `tools.{tool}.placeholder` |
| Labels | "Longitud:", "Formato:" | `tools.{tool}.{label}` |
| Messages | "JSON válido", "Error en línea 5" | `tools.{tool}.messages.*` |
| Badges/Tags | "Seguras", "Lotes" | `tools.{tool}.tags[]` |
| Info sections | Security tips, explanations | `tools.{tool}.info.*` |

### Phase 3: Migrate Components

#### For Composition API components (`<script setup>`):

```vue
<script setup>
import { useI18n } from 'vue-i18n'
const { t } = useI18n()
</script>

<template>
  <!-- Before -->
  <h1>JSONLint</h1>
  <button>Validar</button>

  <!-- After -->
  <h1>{{ t('tools.jsonlint.name') }}</h1>
  <button>{{ t('common.validate') }}</button>
</template>
```

#### For Options API components (TimestampConverter, PasswordGenerator, etc.):

**Option A (recommended): Migrate to Composition API first**, then use `useI18n()`.

**Option B: Use `this.$t()` directly** (works with `globalInjection: true`):

```vue
<template>
  <h1>{{ $t('tools.timestamp.name') }}</h1>
  <button>{{ $t('common.convert') }}</button>
</template>

<script>
export default {
  methods: {
    showMessage() {
      alert(this.$t('tools.timestamp.messages.converted'))
    }
  }
}
</script>
```

#### Special Patterns in This Project

**Interpolation (counts, values):**
```vue
{{ t('home.subtitle', { count: tools.length }) }}
```

**Dynamic tool data in App.vue** (the `tools` array):
```javascript
// The tools array in App.vue should use t() for names
const tools = computed(() => [
  { path: "/jsonlint", name: t('tools.jsonlint.name'), emoji: "\uD83D\uDD0D" },
  // ...
])
```

**Conditional messages:**
```vue
{{ t(isValid ? 'common.valid' : 'common.invalid') }}
```

**Attributes:**
```vue
<input :placeholder="t('tools.jsonlint.placeholder')" />
<button :title="t('nav.darkMode')">...</button>
```

### Phase 4: Add Language Switcher

Add a language toggle to `App.vue` near the theme toggle:

```vue
<select v-model="locale" @change="changeLocale" class="...">
  <option value="es">ES</option>
  <option value="en">EN</option>
</select>
```

```javascript
import { useI18n } from 'vue-i18n'
const { locale } = useI18n()

const changeLocale = () => {
  localStorage.setItem('locale', locale.value)
}
```

### Phase 5: Validate

1. **Search for remaining hardcoded strings:**
   ```bash
   # In every .vue file, search for Spanish text in templates
   Grep: ">[A-ZÁÉÍÓÚÑ][a-záéíóúñ]" in src/**/*.vue
   # Check for string literals in attributes
   Grep: "placeholder=\"[A-Z]|title=\"[A-Z]" in src/**/*.vue
   ```

2. **Verify JSON files:**
   - Both `es.json` and `en.json` have identical key structures
   - No missing keys between languages
   - Valid JSON syntax

3. **Test:**
   - Switch to English, verify ALL text changes
   - Switch back to Spanish, verify ALL text reverts
   - Check console for vue-i18n warnings about missing keys
   - Test all 10 tools in both languages

---

## Files to Touch (Complete List)

| File | Action |
|------|--------|
| `package.json` | Add `vue-i18n` dependency |
| `src/i18n/index.js` | **Create** - i18n config |
| `src/i18n/locales/es.json` | **Create** - Spanish translations |
| `src/i18n/locales/en.json` | **Create** - English translations |
| `src/main.js` | Import and use i18n plugin |
| `src/App.vue` | Migrate nav strings, add locale switcher |
| `src/views/Home.vue` | Migrate hero + card strings |
| `src/views/JSONLint.vue` | Migrate all UI strings |
| `src/views/JSONSchemaValidator.vue` | Migrate all UI strings |
| `src/views/JSONCompare.vue` | Migrate all UI strings |
| `src/views/UUIDGenerator.vue` | Migrate all UI strings |
| `src/views/PasswordGenerator.vue` | Migrate (Options API or convert) |
| `src/views/TimestampConverter.vue` | Migrate (Options API or convert) |
| `src/views/Base64Converter.vue` | Migrate (Options API or convert) |
| `src/views/URLConverter.vue` | Migrate (Options API or convert) |
| `src/views/ColorPaletteGenerator.vue` | Migrate (Options API or convert) |
| `src/views/PHPSerializer.vue` | Migrate (Options API or convert) |

---

## Quality Standards

- [ ] `vue-i18n` installed and configured
- [ ] All user-facing strings use `t()` or `$t()`
- [ ] Zero hardcoded Spanish/English strings in templates
- [ ] Both `es.json` and `en.json` are complete and valid
- [ ] Language switching works across all views
- [ ] No console warnings for missing translation keys
- [ ] `localStorage` persists language preference
- [ ] Interpolation works correctly for dynamic values
- [ ] Tool names in App.vue `tools` array are translated

---

## Estimated Effort

| Phase | Time |
|-------|------|
| Phase 1: Setup | 10 min |
| Phase 2: Extract strings (~250) | 30-45 min |
| Phase 3: Migrate 13 components | 45-60 min |
| Phase 4: Language switcher | 10 min |
| Phase 5: Validation | 15 min |
| **Total** | **~2 hours** |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/raulfdeztdo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
