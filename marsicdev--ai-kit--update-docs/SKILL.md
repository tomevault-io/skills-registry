---
name: update-docs
description: Generate and maintain project documentation. Use when initializing docs for a new project, updating architecture docs, or creating feature specifications. Creates ai_docs/ and ai_specs/ folders with structured documentation. Use when this capability is needed.
metadata:
  author: marsicdev
---

# Documentation Manager

Manage project documentation in `ai_docs/` (system docs) and `ai_specs/` (feature specs).

## Folder Structure

### `ai_docs/` - System Documentation

Technical documentation about the current state of the system:

| File | Purpose |
|------|---------|
| `README.md` | Index of all documentation |
| `architecture.md` | Project structure and tech stack |
| `database.md` | Data models and schema |
| `api.md` | API endpoints and contracts |
| `sop/adding-routes.md` | SOP: How to add new routes |
| `sop/migrations.md` | SOP: Database migrations |
| `sop/deployment.md` | SOP: Deployment process |

### `ai_specs/` - Feature Specs & Plans

PRDs and implementation plans for features:

| File | Purpose |
|------|---------|
| `README.md` | Index of all specs with status |
| `features/feature-name.md` | Feature spec/PRD |
| `plans/feature-name-plan.md` | Implementation plan |

## Commands

### Initialize Documentation

When asked to initialize documentation:

1. Deep scan the codebase to understand the full architecture
2. Create `ai_docs/` with:
   - `architecture.md` - Project goal, folder structure, tech stack, state management, routing, theming
   - `database.md` - Data models, local storage, remote database schema
   - `README.md` - Index of all docs
3. Create `ai_specs/` with:
   - `README.md` - Empty index, ready for future specs

### Update Documentation

When asked to update documentation:

1. Read `ai_docs/README.md` first to understand existing docs
2. Update relevant sections in architecture or add new SOP files
3. Always update the `README.md` index after changes
4. Keep specs in `ai_specs/` separate from system docs

## Documentation Content

Include these in architecture docs (adapt based on project type):

- State management approach
- Navigation/routing structure
- Dependency injection setup
- Build configurations/environments
- Key packages and their purposes
- Code generation tools (if any)
- Testing strategy

## Naming Conventions

- Use **kebab-case** for all file names: `user-auth.md`, not `userAuth.md`
- Feature specs match feature name: `ai_specs/features/dark-mode.md`
- Plans reference the feature: `ai_specs/plans/dark-mode-plan.md`
- SOPs describe the action: `sop/adding-routes.md`

## Guidelines

- Keep documentation consolidated - prefer fewer comprehensive files over many small ones
- Include a "Related Docs" section at the bottom linking to relevant documentation
- Use clear headings and bullet points for scanability
- Include code examples where helpful
- Reference specific file paths when documenting architecture

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/marsicdev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
