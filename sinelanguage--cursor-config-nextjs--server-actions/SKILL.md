---
name: next-server-actions
description: Build typed Server Actions for forms and mutations. Use when this capability is needed.
metadata:
  author: sinelanguage
---
# Next.js Server Actions

Create Server Actions for form submissions and mutations with explicit
validation and security.

## When to Use

- Mutations co-located with App Router pages
- Forms requiring server-side validation
- CSRF protection requirements

## Inputs

- Form fields and validation rules
- Redirect or revalidation behavior
- CSRF strategy (token or double-submit cookie)

## Instructions

1. Create a Server Action in the route or `actions` module.
2. Validate inputs on the server.
3. Enforce CSRF protection for state-changing actions.
4. Return typed results and handle errors.
5. Use revalidation (`revalidatePath`/`revalidateTag`) if needed.

## Output

- Server Action with validation, CSRF controls, and typed results.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sinelanguage) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
