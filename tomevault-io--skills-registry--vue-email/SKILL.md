---
name: vue-email
description: Use when creating HTML email templates with Vue components — welcome emails, password resets, notifications, order confirmations, newsletters, or transactional emails.
metadata:
  author: AlexanderOpran
---

# Vue Email

Build and send HTML emails using Vue 3 SFC components via [vue-email](https://vuemail.net). Templates are server-only, rendered to HTML strings by `@vue-email/render` and sent through Resend.

## Packages

- `@vue-email/components` — Vue component library (Html, Body, Button, Text, etc.)
- `@vue-email/render` — Server-side render function: `render(component, props?, options?) → Promise<string>`

## Project Structure

```
server/
├── emails/
│   ├── components/          # Reusable email building blocks (.vue)
│   │   ├── EmailButton.vue
│   │   └── EmailLayout.vue
│   ├── templates/           # Full email templates (.vue)
│   │   ├── VerificationEmail.vue
│   │   └── PasswordResetEmail.vue
│   └── theme.ts             # Shared colors, logo URL
├── lib/
│   └── email.ts             # sendEmail(), sendVerificationEmail(), sendPasswordResetEmail()
```

## Writing an Email Template

Email templates are standard Vue 3 SFCs with `<script setup>` and `<template>` blocks.

### Basic Template

```vue
<script setup lang="ts">
import { Body, Container, Head, Html, Preview, Tailwind, Text } from '@vue-email/components'

defineProps<{
  name: string
}>()
</script>

<template>
  <Html lang="en">
    <Tailwind :config="{ theme: { extend: { colors: { brand: '#007bff' } } } }">
      <Head />
      <Preview>Welcome to the platform</Preview>
      <Body class="bg-gray-100 font-sans py-10">
        <Container class="max-w-[600px] mx-auto px-4">
          <Text class="text-base text-gray-800">
            Hi {{ name }}, thanks for signing up!
          </Text>
        </Container>
      </Body>
    </Tailwind>
  </Html>
</template>
```

### Available Components

All imported from `@vue-email/components`:

**Structure:** `Html`, `Head`, `Body`, `Container`, `Section`, `Row`, `Column`, `Tailwind`
**Content:** `Preview`, `Heading`, `Text`, `Button`, `Link`, `Img`, `Hr`
**Specialized:** `CodeBlock`, `CodeInline`, `Markdown`, `Font`

### Component Naming

- Import as PascalCase: `import { Html, Button, Text } from '@vue-email/components'`
- Use in templates as PascalCase: `<Html>`, `<Button>`, `<Text>`
- Props use Vue conventions: `:href="url"`, `:src="imageUrl"`
- Slots replace React's `children`: use `<slot />` for content projection

## Rendering

The `render` function from `@vue-email/render` takes a Vue component and optional props:

```ts
import { render } from '@vue-email/render'
import WelcomeEmail from '../emails/templates/WelcomeEmail.vue'

// Render to HTML
const html = await render(WelcomeEmail, { name: 'John' })

// Render to plain text
const text = await render(WelcomeEmail, { name: 'John' }, { plainText: true })
```

## Sending via Resend

We use `html` (not `react`) when sending through Resend, since vue-email renders to an HTML string:

```ts
const html = await render(component, props)
await client.emails.send({ from, to, subject, html })
```

## Styling

### Tailwind CSS

Wrap content in `<Tailwind :config="...">` for utility class support. Place `<Head />` inside `<Tailwind>`.

```vue
<Tailwind :config="{ theme: { extend: { colors: { red-panda: theme.colors } } } }">
  <Head />
  <!-- content -->
</Tailwind>
```

### Email Client Limitations

- Never use SVG or WEBP images — use PNG/JPG only
- Never use flexbox or grid — use Row/Column components or tables
- Never use responsive breakpoints (`sm:`, `md:`, `lg:`) — not supported in email clients
- Never use theme selectors (`dark:`) — not supported
- Always specify border type (`border-solid`, `border-dashed`, etc.)
- Always add `box-border` on `Button` components
- Always add `border-solid` (or equivalent) on `Hr` and border utilities

### Inline Styles as Fallback

For critical styles that must render in all clients, use `:style` bindings alongside classes:

```vue
<Body
  class="bg-red-panda-background font-sans py-10 m-0"
  :style="{ backgroundColor: theme.colors.background }"
>
```

## Conventions

- Templates are Vue SFCs in `server/emails/templates/` — PascalCase filenames
- Reusable components in `server/emails/components/` — PascalCase filenames
- Theme/config in `server/emails/theme.ts`
- The `email.ts` module in `server/lib/` owns the render + send logic
- Never use template literal variables (`{{variableName}}`) — use Vue's `{{ prop }}` interpolation
- Keep emails under 102KB (Gmail clips larger emails)
- Always provide plain text fallback for accessibility
- Max-width ~600px for mobile compatibility

---
> Source: [AlexanderOpran/nuxt-build-issue](https://github.com/AlexanderOpran/nuxt-build-issue) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-19 -->

---
> Source: [tomevault-io/skills-registry](https://github.com/tomevault-io/skills-registry) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-07 -->
