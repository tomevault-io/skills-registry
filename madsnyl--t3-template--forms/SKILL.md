---
name: forms
description: Build client-side forms using shadcn/ui Form + react-hook-form + Zod + tRPC mutations / server-actions, with router navigation/refresh and Sonner toasts. Enforces strong typing via Zod inputs and Prisma payload types from the Prisma type-settings skill. Use when this capability is needed.
metadata:
  author: madsnyl
---

# Client Forms Skill: shadcn/ui + React Hook Form + Zod + tRPC + Sonner

You implement **client components** that render forms using **shadcn/ui Form** + **react-hook-form** with **Zod validation**, submit via **tRPC mutations**, and provide UX feedback with **Sonner toasts** plus **Next.js router navigation + refresh**.

This skill assumes:
- Inputs are typed from Zod schemas (e.g. `type UpdateEventInput = z.infer<typeof updateEventSchema>`).
- Entity props are typed using Prisma payload types aligned with the [**Prisma type-settings skill**](@.claude/skills/types/SKILL.md) (Prisma.validator + GetPayload).

---

## When to use this skill
Use this skill when the user asks to:
- create/edit forms in Next.js client components
- wire Zod schemas to react-hook-form
- submit via tRPC `.useMutation()` with success/error handling
- show toast confirmation and refresh/redirect afterwards
- ensure the form’s types match Zod input + Prisma payload selection types

---

## Canonical stack & imports
- `"use client";` at the top
- `react-hook-form` + `@hookform/resolvers/zod` for validation
- shadcn/ui components:
  - `Form`, `FormField`, `FormItem`, `FormLabel`, `FormControl`, `FormMessage`, `FormDescription`
  - `Input`, `Textarea`, `Select` primitives
- `api` from `~/trpc/react`
- `toast` from `sonner`
- `useRouter` from `next/navigation`

---

## Hard rules (must follow)
1. **Zod-first typing**
   - Use `zodResolver(schema)` and a form generic of the inferred input type:
     - `useForm<UpdateEventInput>({ resolver: zodResolver(updateEventSchema), ... })`
2. **No inline business logic in UI**
   - The component handles form state + calling tRPC.
   - Domain logic belongs in server (services) or tRPC controllers.
3. **Mutation UX contract**
   - `onSuccess`: show success toast + navigate (optional) + refresh data.
   - `onError`: show error toast with `error.message`.
4. **Disable submit while pending**
   - Use `mutation.isPending` to disable submit and show loading text.
5. **Entity prop types reference Prisma type-settings skill**
   - Component props (`event`, `user`, etc.) must be typed via Prisma payload types derived from shared `select/include` definitions.
   - Do not hand-write “Event shape” interfaces that drift from Prisma selections.

---

## File conventions
- Zod schemas live in:
  - `@src/schemas/<domain>.ts` (or domain folder)
- Prisma payload selections/types live in:
  - `@src/types/<domain>/...` (per Prisma type-settings skill)

---

## Standard form structure (the canonical pattern)

### 1) Type the entity prop using Prisma payload types
Preferred:
- Keep the select/include in `types/<domain>/...`
- Use `Prisma.<Model>GetPayload<{ select: typeof ... }>` or `GetPayload<typeof args>`

Example:
```ts
import type { Prisma } from "generated/prisma";
import { EventDetail } from "~/types/event";

// EventDetail should be a Prisma.validator() select/include defined in types/
type Event = Prisma.EventGetPayload<{ select: typeof EventDetail }>;
````

### 2) Initialize RHF with Zod + defaultValues from the entity

Rules:

* Always provide default values for every form field you render.
* For optional fields, use `?? ""` for textareas/inputs.

Example:

```ts
const form = useForm<UpdateEventInput>({
  resolver: zodResolver(updateEventSchema),
  defaultValues: {
    id: event.id,
    title: event.title,
    rules: event.rules ?? "",
    // ...
  },
});
```

### 3) Create a tRPC mutation with toast + router flow

Preferred mutation pattern:

* `onSuccess`: toast + route + refresh
* `onError`: toast

Example:

```ts
const router = useRouter();

const updateEvent = api.event.update.useMutation({
  onSuccess: () => {
    toast.success("Event updated successfully!");
    router.push("/admin/events");
    router.refresh();
  },
  onError: (error) => toast.error(error.message),
});
```

### 4) Submit handler calls `.mutate(values)`

```ts
const onSubmit = (values: UpdateEventInput) => updateEvent.mutate(values);
```

### 5) Use shadcn `<Form>` + `<FormField>` for accessibility & errors

* Wrap `<form>` inside `<Form {...form}>`.
* Use `<FormMessage />` on each field.

This is aligned with shadcn’s recommended RHF + Zod pattern.

---

## Cache refresh strategy (choose the right one)

### Default (simple admin flows)

Use:

* `router.push(...)` + `router.refresh()`
  This revalidates the route and refetches server component data.

### When staying on the same page with client queries

Prefer tRPC utils invalidation:

* `const utils = api.useUtils();`
* `await utils.<path>.<proc>.invalidate()`

Example:

```ts
const utils = api.useUtils();

const updateEvent = api.event.update.useMutation({
  onSuccess: async (_, input) => {
    toast.success("Updated!");
    await utils.event.byId.invalidate({ id: input.id });
  },
});
```

Rule of thumb:

* If the page is RSC-driven: `router.refresh()`
* If the page is client-query-driven: `utils...invalidate()`

---

## UX rules

* Submit button:
  * disabled when `mutation.isPending`
  * label changes to “Updating…” / “Saving…”
  * <Loader2 /> lucide icon added to text with spinning animation
* Always show a toast on success and error (Sonner).
* Provide a “Cancel” button that navigates back if it is inside a confirmation dialog.

---

## Anti-patterns (do not do)

* ❌ Missing schema validation (no `zodResolver`)
* ❌ Manually typing inputs instead of `z.infer<typeof schema>`
* ❌ Passing untyped/unknown entity shapes to the form component
* ❌ Letting `sortBy`, enum values, or select options be arbitrary strings (must be schema-driven/whitelisted)
* ❌ Doing server writes directly in the component (use tRPC mutation)
* ❌ Forgetting to disable submit while pending

---

## What to output when asked to build a new form

Provide:

1. Zod schema + inferred input type (or confirm existing schema)
2. Prisma selection type for the entity prop (per Prisma type-settings skill)
3. Full form component (client) using shadcn Form + RHF
4. tRPC mutation wiring with toast + refresh/navigation
5. Any invalidation plan (`router.refresh` vs `useUtils().invalidate`)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/madsnyl) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
