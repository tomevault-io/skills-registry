---
name: tanstack-start-react-architect
description: Architect, plan, and implement TanStack Start React applications and refactors. Use when designing routing structure, SSR/SPA/SSG/ISR strategy, loaders/server functions/routes, middleware/context, execution boundaries, authentication, environment variables, hosting, SEO/LLMO, observability, or when migrating to TanStack Start and needing a full-stack architecture plan plus code scaffolds. Use when this capability is needed.
metadata:
  author: andymarigoldlabs
---

# TanStack Start React Architect

## Mission
Design a complete TanStack Start architecture and deliver actionable plans or code changes that follow Start/Router idioms, execution boundaries, and hosting realities. Prioritize correctness, SSR safety, and clean boundaries between server and client.

## Workflow
1) Clarify scope and constraints
- Ask for missing requirements only if blocking (max 5 targeted questions).
- Determine SSR needs, auth model, data sources, deployment target, and SEO/LLMO expectations.

2) Produce an architecture brief
- Use `assets/templates/architecture-brief.md` as the structure.
- Fill in a route map, rendering strategy, data loading plan, and boundary decisions.

3) Route and data design
- Define route tree, layouts, and loaders before components.
- Prefer loader data for navigation-critical data and SSR hydration.
- Use server functions for typed RPC; use server routes for external callbacks/webhooks or public APIs.

4) Choose rendering strategy
- Default to SSR unless there is a strong reason not to.
- Use Selective SSR or SPA mode only for routes that cannot render deterministically on the server.
- Plan static prerendering and ISR via cache headers when content is mostly static.

5) Enforce execution boundaries
- Treat loaders as isomorphic (server + client). Never place secrets directly in loaders.
- Use environment functions to isolate server-only and client-only logic.
- Guard hydration-prone UI with `ClientOnly`, cookies, or selective SSR.

6) Middleware and context
- Use request middleware for cross-cutting concerns (logging, auth, headers).
- Use server function middleware for validation, client headers, and shared context.

7) Security and authentication
- Select partner/OSS/DIY approach early.
- Use server sessions (HTTP-only cookies) for most web apps.
- Protect routes with `beforeLoad` and guard server functions regardless of client checks.

8) Environment and config
- Keep secrets server-only (`process.env`), expose public values with `VITE_`.
- Add `env.d.ts` typing and runtime validation where needed.

9) Deployment and hosting
- Align build output and runtime with the chosen host (Nitro, Cloudflare, Netlify, etc.).
- Set cache headers for ISR and CDN behavior explicitly.

10) SEO and LLMO
- Populate `head` meta, OpenGraph/Twitter, and JSON-LD.
- Provide sitemap/robots/llms endpoints as needed.

11) Observability
- Add request logging, error boundaries, and performance metrics.
- Integrate Sentry or another tool if required.

12) Validate with an architecture inventory
- Run `scripts/scan_start_arch.py --root .` to inventory routes, SSR overrides, and key patterns.
- Use the output to verify boundaries and missing configuration.

## Output expectations
- Provide a concise architecture brief plus a concrete implementation plan.
- If code changes are requested, implement minimal diffs using Start idioms and note risks.
- Call out SSR/hydration risks and server/client boundary violations explicitly.

## Templates (assets)
- `assets/templates/architecture-brief.md`: Architecture plan skeleton.
- `assets/templates/root-route.tsx`: Root route document shell.
- `assets/templates/route.tsx`: Standard route with loader/head.
- `assets/templates/start.ts`: `createStart` defaults + middleware wiring.
- `assets/templates/server-entry.ts`: Server entry scaffold.
- `assets/templates/client-entry.tsx`: Client hydration scaffold.
- `assets/templates/server-function.ts`: Typed server function pattern.
- `assets/templates/server-route.ts`: Server route pattern.
- `assets/templates/middleware.ts`: Request + server function middleware patterns.
- `assets/templates/env.d.ts`: Environment typing starter.
- `assets/templates/seo-head.ts`: SEO/JSON-LD head example.

## Script
- `scripts/scan_start_arch.py`: Inventory Start architecture signals (routes, SSR overrides, middleware, config).

## Reference map (open only what you need)
- `references/skill-reference.md`: Skill format, triggers, and packaging guidelines.
- `references/overview.md`: Start overview, capabilities, and framework comparison.
- `references/routing.md`: File-based routing, root route, HeadContent/Scripts/Outlet.
- `references/execution-model.md`: Isomorphic execution model and boundary APIs.
- `references/environment-variables.md`: Env loading, VITE_ rules, runtime validation.
- `references/server-functions.md`: Server functions, validation, context utilities, redirects.
- `references/static-server-functions.md`: Build-time cached server functions (experimental).
- `references/environment-functions.md`: createIsomorphicFn / createServerOnlyFn / createClientOnlyFn.
- `references/middleware.md`: Request and server function middleware composition and precedence.
- `references/error-boundaries.md`: Router error boundaries and reset flow.
- `references/server-routes.md`: API routes, handlers, params, and middleware.
- `references/hydration-errors.md`: Hydration mismatch causes and fixes.
- `references/selective-ssr.md`: Per-route SSR, inheritance, and shell rendering.
- `references/spa-mode.md`: SPA shell, redirects, and server function routing.
- `references/static-prerendering.md`: Prerender settings, crawling, and filtering.
- `references/isr.md`: Cache headers, CDN behavior, and revalidation patterns.
- `references/server-entry-point.md`: Server entry, createStartHandler, request context.
- `references/client-entry-point.md`: StartClient hydration patterns.
- `references/hosting.md`: Cloudflare, Netlify, Nitro, Vercel, Node, Bun, Appwrite.
- `references/authentication-overview.md`: Auth architecture options and tradeoffs.
- `references/authentication.md`: DIY auth flows, sessions, RBAC, security, tests.
- `references/databases.md`: Database integration patterns and provider options.
- `references/observability.md`: Logging, metrics, Sentry, New Relic, OpenTelemetry.
- `references/path-aliases.md`: TypeScript and Vite path aliases.
- `references/tailwind-integration.md`: Tailwind v4/v3 setup in Start.
- `references/rendering-markdown.md`: Markdown pipeline and static/dynamic strategies.
- `references/seo.md`: SEO head tags, sitemaps, robots, structured data.
- `references/llm-optimization.md`: LLMO/AI optimization patterns and llms.txt.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/andymarigoldlabs) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
