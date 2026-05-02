---
name: clerk-custom-auth-ui-nextjs
description: Clerk + Next.js patterns that extend the official skills with custom auth flows, UI component configuration and routing strategies. Use when this capability is needed.
metadata:
  author: nicolas-angelo
---

# Sign-In-Or-Up with Intercepting Route Modals

One `<SignIn withSignUp />` component handles both sign-in and sign-up. No dedicated sign-up page required.

## References

| Reference | What it covers |
|-----------|---------------|
| `references/sign-in-or-up-flow.md` | CRITICAL - `withSignUp` prop, SignInCard component, env vars |
| `references/auth-modal.md` | HIGH - Intercepting route modal pattern, parallel routes, fallback page |

## Mental Model

```
User clicks "Sign In" link
  ├── Soft navigation → intercepting route → modal overlay
  └── Hard navigation (direct URL) → dedicated (auth) page
```

Both render the same `<SignInCard />` component. The modal wraps it in a `<Dialog>`. The dedicated page centers it on screen. One component, two contexts.

## Key Decisions

1. **`withSignUp` on `<SignIn>`** eliminates the need for a separate `/sign-up` route entirely
2. **Intercepting routes `(.)`** show sign-in as a modal on soft nav while preserving the background page
3. **`@modal` parallel route** in root layout renders alongside `children` — both are always present
4. **`<Show when="signed-out">`** gate prevents the modal from rendering when already authenticated

## File Tree

```
app/
├── layout.tsx                          # Accepts { children, modal } — renders both
├── @modal/
│   ├── default.tsx                     # Returns null (required for parallel routes)
│   └── (.)sign-in/
│       └── [[...sign-in]]/
│           ├── page.tsx                # Wraps SignInCard in <Show> + <Modal>
│           └── modal.tsx               # Dialog component with router.back() on close
└── (auth)/
    ├── layout.tsx                      # Centered container layout
    └── sign-in/
        └── [[...sign-in]]/
            └── page.tsx                # Renders <SignInCard /> directly
components/
└── sign-in-card.tsx                    # Single source of truth for <SignIn> config
```

## Docs

- [`<SignIn />` props reference](https://clerk.com/docs/nextjs/reference/components/authentication/sign-in)
- [Custom sign-in-or-up page guide](https://clerk.com/docs/nextjs/guides/development/custom-sign-in-or-up-page)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nicolas-angelo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
