---
name: nextjs-project-starter
description: Creates Next.js projects with a configurable stack (Mantine, Supabase, Zustand, Zod). This skill should be used when the user says "create a Next.js project", "new web project", "bootstrap fullstack app", "start new app", "crear proyecto Next.js", "nuevo proyecto web", "empezar app fullstack", or wants to scaffold a new personal project from scratch.
metadata:
  author: testacode
---

# Next.js Project Starter

Create new Next.js projects with a configurable stack, always using latest stable versions with documentation verification.

## Core Principles

1. **Always ask before acting** - Never assume, always confirm
2. **Latest versions** - Search npm/docs for current stable versions
3. **Show breaking changes** - Warn about major version changes
4. **Configurable** - Let user choose what to include

## Workflow

### Phase 1: Project Basics

Ask user for:
1. Project name (kebab-case)
2. Project description (one line)
3. Target domain (custom domain / none)

### Phase 2: Node Version Setup

```bash
node --version
```

Present options: use current, use Next.js recommended LTS, or specify custom version.

Generate:
- `.nvmrc` with selected version
- `package.json` with `"engines": { "node": ">=X.X.X" }`

### Phase 3: Feature Selection

Present configurable features:

```
Core Features (select all that apply):
- Mantine UI + Tabler Icons
- Supabase (auth + database)
- Zustand (state management)
- Zod (validation)
- SWR (data fetching)
- TanStack Query (alternative to SWR)
- Vercel Analytics + Speed Insights
- Testing setup (Vitest + Testing Library)
```

### Phase 4: Version & Docs Verification

For EACH selected feature:

```bash
npm view @mantine/core version
```

Search for official documentation, migration guides, and known issues via WebSearch.

Present version info and ask for confirmation before installing. **IMPORTANT**: Do this for EACH dependency before installing. Never batch install without verification.

### Phase 5: Integration Questions

After features are confirmed, ask about integrations:
1. GitHub Repository (create private repo via `gh repo create`)
2. Vercel Deployment (link via `vercel link`)
3. Supabase Project (if selected)
4. CLAUDE.md generation

### Phase 6: Project Creation

```bash
npx create-next-app@latest {project-name} \
  --typescript \
  --eslint \
  --no-tailwind \
  --app \
  --src-dir \
  --import-alias "@/*"
```

Then install selected dependencies with verified versions.

### Phase 7: Structure Generation

Create preferred folder structure. See `references/folder-structure.md` for the full tree.

### Phase 8: Configuration Files

Generate based on selections:

- **Mantine**: See `references/mantine-setup.md` for PostCSS config and provider setup
- **Supabase**: See `references/supabase-setup.md` for client/server/middleware setup
- **Environment template**: Generate `.env.example` with required vars for selected features

### Phase 9: CLAUDE.md Generation

If user confirmed, generate CLAUDE.md with:
- Stack overview with versions
- Available commands
- Project structure
- Key patterns (Server Components, App Router, etc.)

### Phase 10: Final Summary

Present summary with: project location, node version, installed packages with versions, integration links, and next steps.

## Dependencies Reference

When searching for versions, use these as baseline:

| Package | Purpose | Docs URL |
|---------|---------|----------|
| next | Framework | https://nextjs.org/docs |
| @mantine/core | UI Components | https://mantine.dev |
| @mantine/hooks | React Hooks | https://mantine.dev/hooks |
| @tabler/icons-react | Icons | https://tabler.io/icons |
| @supabase/ssr | Supabase SSR | https://supabase.com/docs |
| zustand | State | https://zustand-demo.pmnd.rs |
| zod | Validation | https://zod.dev |
| swr | Data Fetching | https://swr.vercel.app |
| @tanstack/react-query | Data Fetching | https://tanstack.com/query |
| vitest | Testing | https://vitest.dev |

## Error Handling

If any step fails:

1. **npm install fails**: Check Node version, try with `--legacy-peer-deps`
2. **gh repo create fails**: Verify `gh auth status`
3. **vercel link fails**: Run `vercel login` first
4. **Supabase setup fails**: Guide user to manual setup at supabase.com

Always ask user how to proceed on errors, never assume.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/testacode) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
