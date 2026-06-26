---
name: update-website
description: Sync aiagentflow.dev with the latest CLI version — providers, agents, docs, version numbers. Use when this capability is needed.
metadata:
  author: aiagentflow
---

# Update aiagentflow.dev

Keeps the website at `/media/raj/Work1/aiagentflow.dev` in sync with the current state of the CLI.
Run this skill after every release to make sure the site reflects what's actually shipped.

## Step 1 — Read the current CLI version

```bash
node -p "require('./package.json').version"
```

Note the version. All version references on the site should match.

## Step 2 — Check what changed since last release

```bash
git log --oneline $(git describe --tags --abbrev=0 HEAD^)..HEAD
```

Skim the log for:
- New providers added → update Providers section
- New agents added → update Agent Roles doc
- New CLI commands → update Getting Started / docs
- Breaking config changes → update Configuration doc
- Bug fixes worth calling out → consider a blog post

## Step 3 — Update the providers section

**Files to touch:**
- `/media/raj/Work1/aiagentflow.dev/messages/en.json` — `Providers` key
- `/media/raj/Work1/aiagentflow.dev/components/Providers.tsx` — one card per provider

**Check against the source of truth:**
```bash
cat src/providers/metadata.ts
```

For each provider in `PROVIDER_DESCRIPTIONS`:
- Is there a card in `Providers.tsx`? If not, add one.
- Is the description in `en.json` accurate? Update if stale.
- Remove any "coming soon" text for providers that now exist.

Grid layout: use `lg:grid-cols-3` for 6 providers, `md:grid-cols-2` for 4.

## Step 4 — Update the agent roles doc

**File:** `/media/raj/Work1/aiagentflow.dev/content/docs/agent-roles.md`

**Check against source of truth:**
```bash
cat src/agents/types.ts   # ALL_AGENT_ROLES
ls src/agents/roles/      # one file per agent
```

Every agent in `ALL_AGENT_ROLES` should have its own `##` section in the doc.

## Step 5 — Update the configuration doc

**File:** `/media/raj/Work1/aiagentflow.dev/content/docs/configuration.md`

- Add/remove provider sections to match `src/providers/metadata.ts`
- Update default models from `PROVIDER_DEFAULT_MODELS`
- Make sure the `aiagentflow init` workflow is described accurately

## Step 6 — Update the getting-started doc

**File:** `/media/raj/Work1/aiagentflow.dev/content/docs/getting-started.md`

- Update the version number (`v1.0.0` → new version)
- Update the prerequisites to list all free-tier provider options

## Step 7 — Update feature copy if needed

**File:** `/media/raj/Work1/aiagentflow.dev/messages/en.json` — `Features` key

- `feature6` lists providers by name — keep it in sync with `metadata.ts`
- If new major features shipped (new commands, dry-run, resume, etc.) consider adding/updating a feature card

## Step 8 — Verify the build

```bash
cd /media/raj/Work1/aiagentflow.dev && pnpm build 2>&1 | tail -20
```

Fix any TypeScript or missing-key errors before committing.

## Step 9 — Commit and push

```bash
cd /media/raj/Work1/aiagentflow.dev
git add -A
git commit -m "sync website with CLI vX.Y.Z"
git push
```

---

## Quick reference — what lives where

| What                  | CLI source file                        | Website file                                              |
|-----------------------|----------------------------------------|-----------------------------------------------------------|
| Provider list         | `src/providers/metadata.ts`            | `components/Providers.tsx` + `messages/en.json`           |
| Agent list            | `src/agents/types.ts`, `agents/roles/` | `content/docs/agent-roles.md`                             |
| Provider config guide | `src/core/config/schema.ts`            | `content/docs/configuration.md`                           |
| Version number        | `package.json`                         | `content/docs/getting-started.md`                         |
| Feature descriptions  | general                                | `messages/en.json` → `Features`                           |
| CLI commands          | `src/cli/index.ts`                     | `content/docs/getting-started.md`                         |

---
> Source: [aiagentflow/aiagentflow](https://github.com/aiagentflow/aiagentflow) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-25 -->
