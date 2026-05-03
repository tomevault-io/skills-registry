---
name: vue-component
description: | Use when this capability is needed.
metadata:
  author: chawkitariq
---

# Skill : Créer un composant Vue 3

Quand l'utilisateur demande de créer un composant Vue, applique ce template et ces règles.

## Ressources disponibles

- [`templates/CardItem.vue`](templates/CardItem.vue) — composant carte fidélité complet (modes merchant + client)
- [`templates/CardForm.vue`](templates/CardForm.vue) — formulaire avec validation Zod, UForm, UInputNumber

## Template de base

```vue
<script setup lang="ts">
// 1. Types importés (import type uniquement)
import type { Card } from '~/composables/useDB'

// 2. Props typées avec générics
const props = defineProps<{
  requiredProp: string
  optionalProp?: number
  complexProp: Card
}>()

// 3. Emits typés avec tuple syntax
const emit = defineEmits<{
  updated: [id: string, value: number]
  deleted: [id: string]
}>()

// 4. Composables Nuxt (auto-importés, pas d'import manuel)
const toast = useToast()

// 5. State local
const isLoading = ref(false)

// 6. Computed (logique hors template)
const displayValue = computed(() => props.optionalProp ?? 0)

// 7. Fonctions (async avec try/catch + feedback toast)
async function handleAction() {
  isLoading.value = true
  try {
    // logique métier
    emit('updated', 'id', displayValue.value)
    toast.add({ title: 'Succès', color: 'success' })
  }
  catch {
    toast.add({ title: 'Erreur', color: 'error' })
  }
  finally {
    isLoading.value = false
  }
}
</script>

<template>
  <UCard class="w-full">
    <!-- Contenu principal -->
    <p class="font-semibold text-lg">{{ props.requiredProp }}</p>

    <!-- Actions : UButton avec size="lg" (min touch target 48px) -->
    <UButton
      color="primary"
      size="lg"
      :loading="isLoading"
      @click="handleAction"
    >
      Action
    </UButton>
  </UCard>
</template>
```

## Règles obligatoires

> Structure complète, ordre des blocs, props/emits, composables et JSDoc dans **`coding-standards`**.

**Composants Nuxt UI disponibles :**
- Layout : `UCard`, `UContainer`, `USeparator`
- Actions : `UButton`, `UButtonGroup`
- Formulaire : `UForm`, `UFormField`, `UInput`, `UInputNumber`, `UTextarea`, `USelect`, `UCheckbox`
- Feedback : `UAlert`, `UBadge`, `UProgress`, `UIcon`
- Overlay : `UModal`, `UDrawer`, `UTooltip`
- Icônes : `i-lucide-*` en priorité (ex: `i-lucide-trash-2`, `i-lucide-plus`, `i-lucide-gift`)

**UX mobile :**
- Boutons interactifs : `size="lg"` minimum (touch target 48px)
- Textes tronqués dans les listes : classe `truncate`
- Layouts flexibles : `flex flex-col gap-4` ou `flex gap-2`

**SSR safety :**
- Si le composant accède à `localStorage`, `indexedDB`, `document`, ou `window` → envelopper dans `onMounted`
- Si le composant utilise `qrcode` ou `html5-qrcode` → `<ClientOnly>` + import dynamique dans `onMounted`

## Composable associé (si logique complexe)

Si la logique dépasse ~20 lignes ou est réutilisable, extraire dans `app/composables/useNomFeature.ts` :

```ts
// app/composables/useNomFeature.ts
export function useNomFeature() {
  const state = ref<Type>(initialValue)

  async function doSomething(): Promise<void> {
    // logique
  }

  return { state, doSomething }
}
```

## Nommage des fichiers

- Composant : `PascalCase.vue` → `app/components/MonComposant.vue`
- Page : `kebab-case.vue` → `app/pages/ma-page.vue`
- Route dynamique : `[param].vue` → `app/pages/client/[id].vue`

---

## Confirmation en fin de tâche

> ✅ **vue-component appliqué**
> - Structure : [<script setup lang="ts"> en premier, ordre des blocs respecté]
> - Props/Emits : [générics, tuple syntax]
> - UX mobile : [size="lg" sur boutons interactifs, truncate sur textes longs]
> - SSR safety : [ClientOnly / onMounted si browser API]
> - Tests : [fichier .test.ts co-localisé créé ou mis à jour]

---
> Source: [chawkitariq/fidely](https://github.com/chawkitariq/fidely) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-19 -->
