---
name: scaffold-rest-api
description: Use when creating a new TypeScript REST API, backend endpoint scaffold, health-checked service, or framework-native API project with Nuxt server/api or Next route handlers, GitHub CI, safe provider auth, and Netlify or Railway deployment.
metadata:
  author: seriousownsya
---

# Scaffold Rest Api

## Overview

Create a secure typed REST API with almost no user choices. Default to Nuxt `server/api`, TypeScript, npm, zod validation, private GitHub, GitHub Actions CI, Playwright request tests, and Netlify deployment. Use Railway only when the API needs a persistent server, database, worker, queue, cron, or Railway-specific service.

## Zero-Option Rule

Do not interview the user about backend framework, router, test runner, package manager, CI, or repository visibility. Use these defaults unless the user explicitly asks otherwise:

| Decision | Default |
| --- | --- |
| API framework | Nuxt `server/api` |
| Next support | Only when user asks for Next, React, or App Router |
| REST style | JSON over HTTP with explicit status codes |
| Validation | zod schemas shared between client/server |
| Package manager | npm |
| Repository | private GitHub repository |
| Node runtime | Node 24 LTS |
| UI/CSS | none by default for API-only projects |
| First deploy | Netlify if serverless API is enough |
| Railway | only for database, worker, cron, queue, or persistent service |

Do not scaffold Express, Fastify, Nest, Hono, tRPC, GraphQL, or a separate API server unless the user explicitly overrides the stack.

## Project Name Rule

The user must provide a project name. If no name is present, ask exactly one question: "What should the project be called?" Then stop until they answer. Do not scaffold with placeholder names.

Derive the folder name, npm package name, private GitHub repo, Netlify site name, Railway project name when needed, and visible API title from that name. Normalize to a lowercase slug. If the target folder or provider project name is taken, append a short safe suffix automatically and report the final names.

## Safe Access

Never ask the user to paste provider tokens into chat. Check local auth:

```powershell
gh auth status
netlify status
```

Check Railway only if the decision rule requires Railway:

```powershell
railway whoami
```

If auth is missing, stop for the safe local command only:

```powershell
gh auth login
netlify login
railway login
```

For CI secrets, use `gh secret set`, Netlify dashboard/env UI, or Railway project variables. Do not print, echo, commit, log, or store secrets in source.

Every generated project must include root `AGENTS.md` from `assets/templates/AGENTS.md`. Also include root `CLAUDE.md` from `assets/templates/CLAUDE.md` so Claude Code imports the same instructions. Do not use symlinks on Windows. Keep `AGENTS.md` as the canonical policy for credential handling, verification, and CI follow-up.

## Lightweight Bootstrap Rule

Assume fresh user systems have little installed. For first-run scaffolding, require only `node`, `npm`, `git`, and the provider CLIs needed for the requested deployment: `gh`, `netlify`, and `railway` only when Railway is actually required. Do not require Python, Docker, gitleaks, trivy, semgrep, or Playwright browsers as initial local prerequisites.

Use the generated no-dependency `scripts/check-no-secrets.mjs` for local secret scanning. Treat gitleaks, trivy, and semgrep as maintainer release-validation tools or later hardening tools, not normal end-user bootstrap requirements. Playwright request tests for REST APIs should not install browsers.

## Dependency Gate

Use exact package versions. Do not use `latest`, caret ranges, tilde ranges, or unqualified `npx` package execution.

Before adding each package, run this skill's helper script. Resolve the path relative to this `SKILL.md`, not the generated project:

```powershell
& "<skill-dir>\scripts\select-npm-version.ps1" <package-name>
```

Use the returned `package@version`. The helper selects the newest stable version published at least 7 days ago. Always commit `package-lock.json` and run `npm ci` in CI.

Add `.npmrc`:

```ini
save-exact=true
fund=false
audit=true
```

## Workflow

1. Enforce the Project Name Rule and derive the project slug.
2. Verify `node`, `npm`, `git`, `gh`, and `netlify`. Verify `railway` only when Railway is needed. Use Node 24 LTS unless the project has a documented incompatibility. If a provider CLI is missing, stop with the official install/login command; do not install global tools without user approval.
3. Verify provider auth. Stop only for missing safe CLI login.
4. Scaffold Nuxt by default with exact `nuxi`; use exact `create-next-app` only for explicit Next requests.
5. Add exact versions for API dependencies and test dependencies, including `zod`, Vitest, Playwright, and framework test utilities.
6. Copy API templates from `assets/templates/` and adapt paths if using Next. This includes `AGENTS.md`, `CLAUDE.md`, `.env.example`, `.nvmrc`, `.npmrc`, `scripts/check-no-secrets.mjs`, `nuxt.config.ts`, CI, Netlify, Railway, API, schema, server security middleware, and test templates. Append `gitignore-security.txt` to the generated `.gitignore`.
7. Add scripts: `secret:scan`, `lint`, `typecheck`, `test:unit`, `test:api`, `build`, `dev`, `start`, and `preview`. `secret:scan` must run `node scripts/check-no-secrets.mjs`.
8. Keep both Netlify static headers and the Nuxt server middleware headers. Netlify TOML headers alone do not cover every Nuxt serverless/API response.
9. Run `npm run secret:scan`, `npm run typecheck`, `npm run test:unit`, `npm run build`, and `npm run test:api`.
10. Create a private GitHub repo with `gh repo create --private --source . --remote origin`, commit, and push.
11. Check GitHub Actions after pushing. If CI fails and credentials allow access, inspect logs with `gh run view --log`, fix the issue, rerun local checks, commit, and push again. Do not rely on a nontechnical user to debug red CI.
12. Deploy to Netlify by default:

```powershell
netlify sites:create --name <project-slug> --json
netlify deploy --prod --build
```

13. If Railway is required, add `railway.json`, verify Railway auth, create/link the Railway project, configure variables through Railway, and deploy with `railway up`.

## Project Shape

For Nuxt:

```text
server/
  middleware/
    security-headers.ts
  api/
    health.get.ts
    message.post.ts
  utils/
shared/
  schemas/
    message.ts
tests/
  unit/
  api/
scripts/
  check-no-secrets.mjs
.github/workflows/ci.yml
AGENTS.md
CLAUDE.md
.env.example
.nvmrc
.npmrc
nuxt.config.ts
netlify.toml
railway.json       # only when Railway is needed
```

For explicit Next:

```text
src/
  app/
    api/
      health/route.ts
      message/route.ts
  server/
  shared/
    schemas/
tests/
  unit/
  api/
```

Use Next Route Handlers in `src/app/api/**/route.ts`; do not use legacy Pages API routes for new projects.

## REST Conventions

- Validate all request bodies with zod before using them.
- Export or infer TypeScript types from schemas.
- Return JSON objects with stable keys.
- Include `/api/health` from the first commit.
- Keep secrets server-side only.
- Do not expose stack traces or raw validation internals to clients.
- Add an API test for every endpoint added during scaffolding.

## User-Facing Finish

End with:

- API base URL
- health endpoint URL
- private GitHub repo URL
- where to add an endpoint
- where to add a schema
- how to run locally
- whether Netlify or Railway was used and why
- CI status

Do not include raw tokens, secret values, or unnecessary implementation history.

## Resources

- `scripts/select-npm-version.ps1`: choose exact npm versions older than 7 days.
- `references/rest-api-notes.md`: framework and deployment notes.
- `assets/templates/`: agent policy, secret scan, CI, Netlify, Railway, API, schema, and test templates.

---
> Source: [seriousownsya/vibe-web-starter-skills](https://github.com/seriousownsya/vibe-web-starter-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
