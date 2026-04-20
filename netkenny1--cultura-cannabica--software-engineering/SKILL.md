---
name: software-engineering
description: Guides design and development of websites, apps, CRMs, and other software with focus on visual appeal, modern branding, professional architecture, and stack selection. Use when designing or building software, choosing architectures or tech stacks, comparing backend or app-development tools, or when the user asks about what looks good, what is professional, or what stack to use. Use when this capability is needed.
metadata:
  author: netkenny1
---

# Software Engineering: Design, Architecture & Stack Guidance

## When to Apply This Skill

Use this skill when the user is:
- Designing or building websites, web apps, mobile apps, CRMs, or any software product
- Asking what is visually appealing, on-brand, or professional
- Choosing or comparing architectures, frameworks, or services (backend, hosting, auth, etc.)
- Seeking guidance on coding structure, stacks, or tooling

---

## 1. Visual Design & Human Appeal

**Principles that read as polished and intentional:**

- **Typography**: One clear hierarchy (one primary display/heading font, one body font). Prefer system fonts or established pairings (e.g. Inter + source serif, or a single variable font). Avoid more than two font families; avoid decorative or dated typefaces for body.
- **Spacing & rhythm**: Use a consistent scale (e.g. 4px or 8px base). Generous whitespace; avoid cramped layouts. Align to a grid; consistent padding/margins within components and between sections.
- **Color**: Limit palette (primary, secondary, neutrals, semantic success/warning/error). Ensure contrast for text (WCAG AA minimum). Prefer subtle backgrounds over busy patterns; use color to create hierarchy, not decoration.
- **Hierarchy**: Clear visual order—one main focal point per view, then clear secondary/tertiary. Use size, weight, and color to signal importance.
- **Consistency**: Same patterns for buttons, cards, forms, and navigation across the app. Reuse components; avoid one-off styles.
- **Motion**: Use sparingly (micro-interactions, loading, transitions). Prefer short (200–400ms), purposeful animation; avoid distracting or slow motion.

**Anti-patterns that feel unprofessional:** Cluttered layouts, too many fonts or colors, low-contrast text, inconsistent spacing, broken alignment, heavy gradients or drop shadows without purpose, autoplay or excessive animation.

For deeper patterns and examples, see [reference.md](reference.md#visual-design--ux-patterns).

---

## 2. Modern Branding & On-Brand Aesthetics

**What reads as current and professional today:**

- **Minimalism with intent**: Clean layouts, clear CTAs, enough whitespace. “Minimal” does not mean empty—every element should have a role.
- **Dark mode support**: Offer a dark theme (or system-preference follow) for apps and dashboards; use semantic color tokens so one palette drives both themes.
- **Authenticity**: Avoid generic stock-photo look. Use real imagery, illustration, or abstract visuals that fit the product; consistent tone in copy and visuals.
- **Accessibility as default**: Contrast, focus states, keyboard navigation, and semantic HTML are part of “on brand”—inclusive and professional.
- **Responsive-first**: Design for small screens first or in parallel; layouts and typography scale clearly across breakpoints.
- **Trust signals**: Clear navigation, contact/support, privacy/terms, and consistent identity (logo, favicon, meta) so the product feels legitimate.

**Context matters:** B2B/CRM/dashboards lean clean, data-dense, and efficient. Consumer/marketing sites can be more expressive but should stay coherent and fast. Internal tools prioritize clarity and speed over trendiness.

---

## 3. Professional Architecture & Code Structure

**Architectures to choose from:**

- **Layered (N-tier)**: UI → application/business logic → data access → DB. Good default for CRMs, line-of-business apps, and most web backends. Keeps concerns separated and testable.
- **Clean / Hexagonal**: Core domain and use cases in the center; adapters for UI, DB, and external APIs. Use when domain logic is complex or you expect multiple UIs or integrations.
- **Feature-based**: Organize by feature (e.g. `auth/`, `orders/`, `reports/`) instead of by layer. Fits well with frontend frameworks and medium-to-large apps; scales better than a single “controllers” folder.
- **Monolith first**: Prefer a well-structured monolith for most products. Split into services only when you have clear boundaries, scaling needs, or team boundaries—not by default.

**Universal practices:**

- **Single responsibility**: One clear purpose per module/class/function.
- **Dependency direction**: Dependencies point inward (e.g. UI and DB depend on domain, not the reverse). Inject dependencies where it improves testability.
- **API contracts**: Version and document public APIs; keep internal implementation flexible.
- **Configuration over hardcoding**: Env-based config for URLs, keys, feature flags; no secrets in code.

For more detail on patterns and folder layouts, see [reference.md](reference.md#architecture-patterns).

---

## 4. Stacks: When to Use What

**Frontend (websites & web apps):**

- **React + Next.js**: Full-featured apps, SEO, SSR/SSG, API routes. Strong ecosystem and hiring pool. Default choice for many product teams.
- **Vue + Nuxt**: Similar benefits to Next; often preferred for smaller teams or more template-oriented workflows. Great DX.
- **Svelte + SvelteKit**: Less runtime, fast builds, clean syntax. Good for performance-focused or smaller apps.
- **Plain HTML/CSS/JS or minimal tooling**: Marketing sites, simple pages, or when avoiding a framework is a requirement. Use when complexity is low.

**Backend:**

- **Node.js (Express, Fastify)**: Same language as frontend, huge ecosystem. Good for APIs, real-time, and when team is JS/TS-focused.
- **Python (Django, FastAPI)**: Django for CRMs, admin-heavy apps, and rapid delivery. FastAPI for high-performance APIs and async.
- **Go (Fiber, Echo, stdlib)**: Strong for services, CLI tools, and performance-sensitive backends. Smaller ecosystem but fast and simple deploys.
- **C# / .NET**: Enterprise, Windows integration, strong typing. Good fit for existing Microsoft shops and large systems.

**Mobile:**

- **React Native**: Share logic and many components with web React; one team can do web + mobile. Mature ecosystem.
- **Flutter**: Single codebase for iOS/Android; custom rendering, consistent look. Strong for branded UIs and when you are not tied to web reuse.
- **Native (Swift/Kotlin)**: When you need maximum platform leverage, performance, or platform-specific features; higher cost for two codebases.

**Databases:**

- **PostgreSQL**: Default for relational data (CRMs, accounts, transactions). Robust, JSON support, good tooling.
- **SQLite**: Local/single-instance apps, prototypes, embedded. Not for high-concurrency multi-writer.
- **MongoDB / document DBs**: When schema is fluid or document-shaped; use with care for relational or transactional needs.
- **Redis**: Cache, sessions, queues, real-time. Complement to a primary DB.

For comparison tables and edge cases, see [reference.md](reference.md#stacks-and-frameworks).

---

## 5. Services & Tooling: Backend and App Development

**Backend / BaaS (Backend-as-a-Service):**

- **Supabase**: Open-source Firebase alternative; Postgres, auth, realtime, storage. Good when you want SQL, self-hosting option, and a simple API. Prefer for new projects that need relational data and flexibility.
- **Firebase**: Auth, Firestore, hosting, functions. Strong for rapid prototypes, mobile backends, and when NoSQL and Google ecosystem are a fit. Vendor lock-in and pricing at scale are considerations.
- **Custom backend (Node, Python, Go, etc.)**: Full control, any database, any hosting. Prefer when requirements don’t match BaaS limits or when you need complex logic, compliance, or cost control.

**Auth:**

- **Supabase Auth / Firebase Auth**: Integrated with respective BaaS; quick to ship.
- **Clerk, Auth0, NextAuth**: Dedicated auth services; good for custom backends or when you need many identity providers and enterprise features.
- **Custom (sessions + DB)**: When you need full control or minimal dependencies; more work to secure and maintain.

**Hosting & deployment:**

- **Vercel, Netlify**: Excellent for static and serverless frontends (Next, Nuxt, SvelteKit). Simple CI/CD and previews.
- **Railway, Render, Fly.io**: Good for full-stack apps, databases, and backends; straightforward and cost-effective for small-to-medium.
- **AWS, GCP, Azure**: When you need specific services, compliance, or scale; use only when the complexity is justified.

**When to prefer what:**

- **Speed and simplicity**: Supabase or Firebase + Vercel/Netlify for frontend.
- **Relational data and SQL**: Supabase or custom backend + Postgres.
- **Maximum control and long-term flexibility**: Custom backend + your choice of DB + Railway/Render or cloud.
- **Enterprise or compliance-heavy**: Custom backend + managed DB and auth (e.g. Auth0, Clerk) on a cloud you control.

For a more detailed comparison of services, see [reference.md](reference.md#services-and-tooling).

---

## 6. Quick Decision Guide

| Goal | Suggestion |
|------|------------|
| Marketing or content site | Static or Next/Nuxt/SvelteKit, Vercel/Netlify |
| Web app with auth and data | Next.js or similar + Supabase, or custom API + Postgres |
| CRM / internal tool | Layered backend (e.g. Django or Node) + Postgres + React/Vue admin or dedicated UI |
| Mobile app + web | React Native + shared API, or Flutter; BaaS or custom API |
| Fast prototype | Next.js + Supabase or Firebase |
| High control, no BaaS | Custom backend (Node/Python/Go) + Postgres + frontend of choice |

---

## Summary Checklist

When designing or building software:

- [ ] Visual hierarchy, spacing, and typography are consistent and readable
- [ ] Color and motion support clarity and accessibility
- [ ] Architecture matches complexity (layered or feature-based; monolith unless there is a clear reason to split)
- [ ] Stack fits team, product, and constraints (see tables above)
- [ ] Backend and services chosen for data model (relational → Postgres/Supabase or custom) and control needs (BaaS vs custom)
- [ ] Auth and hosting chosen for speed vs control vs compliance

For extended guidance, examples, and comparisons, use [reference.md](reference.md).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/netkenny1) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
