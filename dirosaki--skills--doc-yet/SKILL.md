---
name: doc-yet
description: Build and maintain high-signal project documentation inside `.agents` for almost any repository, then expose that documentation to Cursor, Claude, Codex, and Gemini through lightweight bridge files. Use whenever the user wants to document a codebase, improve an existing `README.md`, map architecture, explain a monorepo, capture feature boundaries, document a frontend design system or design tokens, create onboarding material, or keep project docs synchronized with the code. Use when this capability is needed.
metadata:
  author: Dirosaki
---

# Doc Yet

## Mission

Turn a real repository into durable, navigable documentation.

- Keep the canonical internal documentation in `.agents`.
- Improve the root `README.md` in place when it exists.
- Generate cross-tool bridge files so Cursor, Claude, Codex, and Gemini can all find the same source of truth.
- Interpret the repository shape first. Do not force the same folder layout onto every project.

## Core workflow

1. Inspect the repository before proposing a documentation structure.
2. Detect the project topology: single app, feature-based app, monorepo, multi-service repo, shared package workspace, or mixed architecture.
3. Ask a few short questions only where choices are still unresolved.
4. Run a gap analysis against existing docs before writing anything.
5. Create or update the minimum useful documentation set in `.agents`.
6. Improve the root `README.md` without flattening all details into it.
7. Generate or refresh bridge files for Cursor, Claude, Codex, and Gemini.
8. Summarize what was created, what was updated, and what remains uncertain.

## Questions to ask after the first inspection

Ask short, concrete questions. Prefer 3-6 questions. Use defaults when the user does not answer all of them.

Always ask:

- Which AI tools are actively used in this repo? Default to `Cursor`, `Claude`, `Codex`, and `Gemini`.
- Who is the main audience for the docs right now: new contributors, current team, AI agents, or external integrators?
- Are there any folders, services, or features that should be excluded from documentation?

Ask when relevant:

- For monorepos: which apps or packages matter most right now?
- For frontend repos: is there a design system, token source, or Storybook-like source of truth that should be documented?
- For backend repos: are there external APIs, queues, workers, or scheduled jobs that must be covered?
- For security-sensitive code: should secrets, internal URLs, or operational details be intentionally omitted?

If the user is unsure, continue with reasonable assumptions and record them in the docs.

## Repository inspection checklist

Inspect before writing:

- Root manifests and workspace manifests: `package.json`, `pnpm-workspace.yaml`, `turbo.json`, `nx.json`, `Cargo.toml`, `go.mod`, `pyproject.toml`, etc.
- App and package boundaries: `apps/`, `packages/`, `services/`, `src/features/`, `modules/`, `libs/`.
- Entrypoints, build tooling, framework config, and deployment config.
- Existing docs: root `README.md`, `docs/`, existing `.agents/`, ADRs, architecture notes.
- API signals: route definitions, OpenAPI files, GraphQL schemas, RPC contracts, client SDKs.
- Data signals: ORM models, schema files, migrations, seed scripts, warehouse models.
- Infra signals: Docker, compose, CI, Terraform, Helm, Vercel, Render, Fly, AWS, GCP, Azure.
- Frontend signals: component libraries, Storybook, theme files, CSS variables, Tailwind config, token JSON, design-system packages.
- Auth and security signals: auth providers, RBAC code, middleware, policy files, permission maps.

Do not document guessed architecture as fact. Mark assumptions clearly.

## Canonical documentation layout

Use `.agents` as the canonical home for internal project documentation, but adapt placement to repository shape.

### Global docs

For the whole repository, keep the canonical global docs in:

- `.agents/project/documentation-index.md`
- `.agents/project/project-overview.md`
- `.agents/project/architecture.md`
- `.agents/project/tech-stack.md`
- `.agents/project/gap-analysis.md`

### Local scoped docs

When a subtree has enough independence or complexity, create a local `.agents` inside that subtree as well.

Common examples:

- `apps/web/.agents/`
- `apps/api/.agents/`
- `packages/ui/.agents/`
- `services/billing/.agents/`
- `src/features/analytics/.agents/`
- `src/domains/orders/.agents/`

Use local `.agents` when the subtree has one or more of these properties:

- Independent ownership or release cadence
- Distinct runtime or deployment model
- Non-trivial public surface area
- Enough internal complexity that future agents would benefit from local context
- A shared UI or design-system boundary

Do not create a local `.agents` for every folder. Prefer fewer, well-linked docs over documentation sprawl.

## Topology rules

Read [references/topology-playbook.md](./references/topology-playbook.md) before deciding placement.

High-level defaults:

- Single-repo, single-deployable app: keep most docs in `.agents/project/`.
- Monorepo: always create `.agents/project/`, then add local `.agents` for meaningful apps, packages, or services.
- Feature-based frontend: create `.agents/project/`, then local `.agents` for major features or domains when they are large enough to deserve their own map.
- Shared UI/design-system package: add a local `.agents` there and document component conventions and tokens.
- Multi-service backend: global docs plus local `.agents` for each important service.

When unsure, prefer this progression:

1. Global `.agents/project/`
2. Local `.agents` only for high-value boundaries
3. Cross-links between global and local indexes

## Required outputs

Always create or update:

- `.agents/project/documentation-index.md`
- `.agents/project/project-overview.md`
- `.agents/project/architecture.md`
- `.agents/project/tech-stack.md`
- `.agents/project/gap-analysis.md`
- Root `README.md`

If `README.md` exists, improve it in place.
If it does not exist, create it in English by default.

Use Mermaid diagrams in `architecture.md` by default when they materially help explain flows, boundaries, or integrations.

## Optional outputs

Read [references/documentation-blueprints.md](./references/documentation-blueprints.md) and generate the subset that matches the repo:

- `repository-map.md`
- `api-contracts.md`
- `data-model.md`
- `integrations.md`
- `auth-and-security.md`
- `deployment-and-environments.md`
- `testing-strategy.md`
- `observability.md`
- `business-rules.md`
- `onboarding.md`
- `troubleshooting.md`
- `glossary.md`
- `adr/`
- `design-system.md`
- `design-tokens.md`

### Frontend-specific expectation

If the repo contains a frontend with shared UI primitives, themes, tokens, or component patterns, strongly prefer documenting:

- `design-system.md`
- `design-tokens.md`
- Relevant local feature docs when the app is feature-based

### README language rule

- Preserve the dominant language of the existing `README.md`.
- If the current README is mostly Portuguese, keep it in Portuguese when improving it.
- If there is no README, default to English.
- The `.agents` docs should be written in English unless the user explicitly asks otherwise.

## Gap analysis rules

Before writing final docs, create or refresh `.agents/project/gap-analysis.md` with:

- Existing docs worth preserving
- Missing docs
- Outdated docs
- Proposed local `.agents` placements
- Open questions
- Assumptions used for the current pass

This file is not a brainstorm dump. Keep it crisp and actionable.

## Content rules

- Prefer verified repository facts over generic best-practice filler.
- Use concrete file paths, commands, frameworks, environments, services, and package names.
- Preserve good existing documentation instead of rewriting for style alone.
- Do not create placeholder sections with `TODO` unless the unknown is important and genuinely unresolved.
- Distinguish clearly between verified facts, likely inferences, and open questions.
- Keep docs navigable: short sections, cross-links, and clear file names.
- Use tables when they improve scanability, not by default.
- When the project is large, summarize details in the global docs and push specifics into local `.agents`.

## README rules

The root `README.md` is the public entrypoint, not the entire internal wiki.

It should usually contain:

- What the project is
- Main apps, services, or packages
- Quick start
- Key commands
- High-level architecture snapshot
- Where to find deeper docs in `.agents`

It should usually not contain:

- Every package detail in a monorepo
- Exhaustive internal architecture prose
- Long operational runbooks better kept in `.agents`

## Cross-tool bridge files

By default, generate or update these bridge files:

- `AGENTS.md`
- `CLAUDE.md`
- `GEMINI.md`
- `.cursor/rules/doc-yet-project-docs.mdc`
- `.claude/commands/doc-yet.md`

Read [references/bridge-templates.md](./references/bridge-templates.md) before writing them.

Bridge file rules:

- Keep them thin. They should point to canonical docs instead of duplicating them.
- Point to `.agents/project/documentation-index.md` first.
- Tell the tool to also read the nearest local `.agents/` when working inside a subtree that has one.
- Preserve any strong existing project instructions; merge instead of overwriting blindly.
- Keep update instructions short and operational: after major architecture or workflow changes, refresh `.agents` and the root `README.md`.

Do not create additional tool-specific command files unless the repo already uses them or the user asks for them.

## Blueprint references

Use these bundled references instead of inventing structure from scratch:

- [references/documentation-blueprints.md](./references/documentation-blueprints.md)
- [references/topology-playbook.md](./references/topology-playbook.md)
- [references/bridge-templates.md](./references/bridge-templates.md)

## Delivery checklist

- The global docs exist in `.agents/project/`.
- Local `.agents` directories exist only where they add real value.
- `architecture.md` includes Mermaid when useful.
- The root `README.md` was improved or created in the correct language.
- Bridge files were created or carefully updated for Cursor, Claude, Codex, and Gemini.
- Frontend repos with real UI systems include design-system and token docs.
- Monorepos link global and local documentation clearly.
- The docs state assumptions instead of presenting guesses as facts.

---
> Source: [Dirosaki/skills](https://github.com/Dirosaki/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
