---
name: vue-component-rules
description: Defines the structure, format, and rules for Vue components in lume-registry. Use when creating or modifying Vue components. Use when this capability is needed.
metadata:
  author: hemia-labs
---

# Vue Component Rules

Rules and format for creating Vue components in lume-registry.

## Directory Structure

```
src/vue/components/
├── ui/                    # All components (simple and compound)
│   ├── button.vue
│   ├── input.vue
│   └── alert.vue         # Single file with all subcomponents
└── meta/                 # Component metadata
    ├── button.meta.json
    └── alert.meta.json
```

## Component Types

### 1. Simple Component (One Vue File)

Location: `src/vue/components/ui/{component}.vue`
Metadata: `src/vue/components/meta/{component}.meta.json`

**Vue file format:**

```vue
<script setup lang="ts">
import { cva, type VariantProps } from "class-variance-authority"
import { cn } from "@/utils/cn"

const componentVariants = cva(
  "base classes with variants",
  {
    variants: {
      variant: {
        default: "default classes",
        destructive: "destructive classes",
        secondary: "secondary classes",
      },
      size: {
        default: "default size classes",
        sm: "small size classes",
        lg: "large size classes",
      },
    },
    defaultVariants: {
      variant: "default",
      size: "default",
    },
  }
)

type ComponentVariants = VariantProps<typeof componentVariants>

const props = defineProps<{
  variant?: ComponentVariants["variant"]
  size?: ComponentVariants["size"]
  type?: "button" | "submit" | "reset"
  class?: string
}>()
</script>

<template>
  <element :class="cn(componentVariants({ variant, size }), props.class)">
    <slot />
  </element>
</template>
```

**Key requirements:**
- Use `<script setup lang="ts">`
- Use `cva` from class-variance-authority for variants
- Use `cn` from `@/utils/cn` for class concatenation
- Define type `ComponentVariants = VariantProps<typeof componentVariants>`
- Use `defineProps<{...}>()` with TypeScript types
- Include props: `variant`, `size`, `class` (and component-specific ones)
- Use `cn()` to combine variants with custom class

### 2. Compound Component (Multiple Subcomponents)

When a component has subcomponents (e.g., Alert, AlertTitle, AlertDescription, AlertAction):
- **All must be in a single `.vue` file**
- Use `defineComponent` + `h()` to create subcomponents
- Export subcomponents from the same file

Location: `src/vue/components/ui/{component}.vue`
Metadata: `src/vue/components/meta/{component}.meta.json`

**Compound Vue file format:**

```vue
<script setup lang="ts">
import { cva, type VariantProps } from "class-variance-authority"
import { defineComponent, h, type PropType } from "vue"
import { cn } from "@/utils/cn"

const componentVariants = cva(...)

type ComponentVariants = VariantProps<typeof componentVariants>

// Subcomponent 1
export const SubComponent1 = defineComponent({
  name: "ComponentSubComponent1",
  props: {
    class: { type: String, default: "" },
  },
  setup(props, { slots }) {
    return () => h("element", { class: cn("classes", props.class) }, slots.default?.())
  },
})

// SubComponent 2
export const SubComponent2 = defineComponent({
  name: "ComponentSubComponent2",
  props: {
    class: { type: String, default: "" },
  },
  setup(props, { slots }) {
    return () => h("div", { class: cn("classes", props.class) }, slots.default?.())
  },
})

// Main component
const props = defineProps<{
  variant?: ComponentVariants["variant"]
  class?: string
}>()
</script>

<template>
  <div :class="cn(componentVariants({ variant }), props.class)" role="alert">
    <slot />
  </div>
</template>
```

### 3. Metadata Format (meta.json)

**For simple components:**
```json
{
  "name": "button",
  "framework": "vue",
  "type": "component",
  "files": ["../ui/button.vue"],
  "registryDependencies": [],
  "dependencies": ["class-variance-authority"],
  "peerDependencies": []
}
```

**For compound components:**
```json
{
  "name": "alert",
  "framework": "vue",
  "type": "component",
  "files": ["../ui/alert.vue"],
  "registryDependencies": [],
  "dependencies": ["class-variance-authority"],
  "peerDependencies": []
}
```

## Dependencies Note

**No incluir dependencias con scope interno en `meta.json`** (ej: `@scope/paquete`). Estas se agregan automáticamente desde el paquete interno del framework.

## Required Props

Every component must accept:
- `class?: string` - additional CSS class
- `variant?: ...` - style variant (default, destructive, etc.)
- `size?: ...` - size (xs, sm, default, lg, etc.)

## Dependencies

- `class-variance-authority` - for variants
- `@/utils/cn` - for class concatenation

---
> Source: [hemia-labs/lume-registry](https://github.com/hemia-labs/lume-registry) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-26 -->
