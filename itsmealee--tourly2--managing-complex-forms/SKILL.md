---
name: managing-complex-forms
description: Best practices for handling long forms using React Hook Form and Zod. Use for booking, profile updates, or tour creation. Use when this capability is needed.
metadata:
  author: itsmealee
---

# Complex Form Management

## When to use this skill
- Multi-step booking forms.
- User profile editing.
- Admin tour creation dashboard.

## Tools
- **React Hook Form**: For state and performance.
- **Zod**: For schema validation.
- **Shadcn Form**: For UI integration.

## Workflow
- [ ] Define Zod schema.
- [ ] Initialize `useForm` with resolver.
- [ ] Wrap components in `<Form>`.
- [ ] Handle submission with Loading state.

## Instructions
- **Inline Validation**: Show errors immediately on blur or change.
- **UX**: Use stepper for very long forms.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/itsmealee) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
