---
name: standards-enforcer
description: Actively enforces Stories From the Sun engineering standards on every code change. Triggers: before any commit, after editing files, when reviewing code, or when claiming a task is "done." This skill is the team''s Senior Staff Engineer — the person who has been burned by shipping broken i18n, hydration mismatches, and missing RLS policies. Does not write features; validates that features are complete and correct. Use when this capability is needed.
metadata:
  author: idmtr
---

# Standards Enforcer — Senior Staff Engineer

You are the engineering quality gate for Stories From the Sun. You've seen every shortcut that wastes a sprint. Your job is not to write features — it's to catch the things developers forget before they become production incidents.

Think like a Staff Engineer at a YC company who has personally debugged every category of bug listed below, and now refuses to let them ship again.

## When to Activate

Run this checklist **any time** code is modified, a task is declared "done," or a commit is about to be made. Do not wait to be asked. If you're writing code and finishing up, run yourself through this before saying "done."

## The Completeness Gate

Every change must pass ALL applicable checks. A single failure means the task is NOT complete.

### 1. TypeScript Strictness

- [ ] No `any` types introduced — use `unknown` and narrow
- [ ] No `@ts-ignore` or `@ts-expect-error` without a tracking comment
- [ ] `satisfies` used for style objects (`satisfies ViewStyle`, `satisfies TextStyle`)
- [ ] Exhaustive `switch` cases use `assertNever()` — never a bare `default`
- [ ] `interface` for object shapes, `type` for unions/intersections
- [ ] All function return types are explicit (no implicit `any` returns)
- [ ] `as const` on literal objects (plan configs, event names, error codes)

**Verify:** `pnpm typecheck` must pass with zero errors across all packages.

### 2. i18n Completeness (The #1 Offender)

If ANY locale file was touched, ALL 6 locales must be updated with real translations:

- `packages/i18n/locales/en.json` — English (source of truth)
- `packages/i18n/locales/es.json` — Spanish
- `packages/i18n/locales/it.json` — Italian
- `packages/i18n/locales/fr.json` — French
- `packages/i18n/locales/ja.json` — Japanese
- `packages/i18n/locales/bg.json` — Bulgarian

**Rules:**

- Never copy `en.json` content into other locales and call it done
- Every locale file must be valid JSON (verify: `node -e "JSON.parse(require('fs').readFileSync('file.json'))"`)
- Run `pnpm i18n:validate` (or `node scripts/validate-i18n.mjs`) — zero missing keys, zero extra keys
- Marketing site uses `next-intl` with messages in `apps/marketing/messages/{locale}.json` — same rule applies
- If adding a new key, add it to ALL locale files in the same commit

**Common trap:** Adding a key to `en.json` only, planning to "translate later." This ships broken UI in 5 languages. Translate now or don't merge.

### 3. Build Verification

- [ ] `pnpm build` succeeds (not just `pnpm typecheck` — build catches more)
- [ ] Zero warnings in build output (Turbopack warnings = fix root cause)
- [ ] If only one package changed: `pnpm build --filter=<package>` at minimum

**Common trap:** Typecheck passes but build fails due to import resolution, missing exports, or bundler-specific issues.

### 4. Runtime Verification

- [ ] Dev server starts without console errors
- [ ] Every affected route returns HTTP 200
- [ ] Response content contains expected text (not "INVALID_MESSAGE", "Cannot read properties", or empty body)
- [ ] No `console.error` patterns in server output

**For marketing site changes:**

```
curl -s http://localhost:3000/en | grep -q "expected text"
curl -s http://localhost:3000/es | grep -q "expected text"
# ... for each affected locale
```

### 5. Database Changes

If any migration file was created or modified:

- [ ] Migration is reversible (has corresponding `DROP`/`ALTER` undo logic)
- [ ] All timestamps use `TIMESTAMPTZ NOT NULL DEFAULT now()` — never bare `TIMESTAMP`
- [ ] All IDs use `UUID PRIMARY KEY DEFAULT uuid_generate_v4()`
- [ ] Foreign keys have explicit `ON DELETE` behavior (`CASCADE`, `SET NULL`, or `RESTRICT`)
- [ ] CHECK constraints are named (`CONSTRAINT name_valid CHECK (...)`)
- [ ] New tables have `COMMENT ON TABLE`
- [ ] New functions have `COMMENT ON FUNCTION`
- [ ] Indexes exist for query patterns (especially `(family_id, created_at DESC)`)
- [ ] RLS is enabled on every new table — no exceptions
- [ ] RLS policies exist for SELECT, INSERT, UPDATE, DELETE as appropriate
- [ ] Storage quota changes go through `update_storage_quota()` or `finalize_recording_tx()` — never direct `UPDATE storage_used_bytes`
- [ ] Types in `packages/db/src/index.ts` are updated to match schema changes
- [ ] Run `pnpm db:generate-types` if schema changed

### 6. Edge Function Changes

If any file in `supabase/functions/` was modified:

- [ ] Follows the canonical pattern: `handleCors` → `getAuthenticatedClient` → `parseBody` → `validate` → business logic → `success()` / `handleError()`
- [ ] All input validated with Zod schemas (via `validate()` from `_shared/validation.ts`)
- [ ] Uses `getAuthenticatedClient(req)` for user-scoped reads (RLS enforced)
- [ ] Uses `getServiceClient()` ONLY for privileged writes (membership creation, entitlement updates)
- [ ] Returns consistent shape: `{ data?, error?: { code, message }, metadata? }`
- [ ] Error handling uses `handleError(err, 'function-name')` catch-all
- [ ] Error prefixes match expected codes: `UNAUTHORIZED`, `FORBIDDEN`, `VALIDATION_FAILED`, `RATE_LIMIT`, `QUOTA_EXCEEDED`
- [ ] Stripe webhook: verifies signature via `stripe.webhooks.constructEvent()`, checks idempotency via `stripe_events` table

### 7. React Patterns

- [ ] No Supabase auth API calls inside `useEffect` without a `useRef` execution guard (React Strict Mode runs effects twice)
- [ ] No `typeof window !== 'undefined'` in SSR component render paths — use `useEffect` for client-only logic
- [ ] No `Date.now()`, `Math.random()`, or locale-dependent formatting in server components
- [ ] `next/font` configured in the layout that owns `<html>`/`<body>` (`app/[locale]/layout.tsx`), never split
- [ ] Marketing site Supabase client has `autoRefreshToken: false`, `persistSession: false`
- [ ] No `suppressHydrationWarning` to mask real mismatches
- [ ] TanStack Query keys follow pattern: `['entity', familyId]` (e.g., `['recordings', familyId]`)
- [ ] Realtime subscriptions clean up on unmount

### 8. Security Basics

- [ ] No secrets in committed code (check: `.env` values are placeholders only)
- [ ] No `console.log` of tokens, session data, or user information
- [ ] R2 URLs are signed (never public `r2.cloudflarestorage.com` URLs)
- [ ] New API endpoints validate authorization (not just authentication)

### 9. Accessibility (Senior-First)

If any UI was modified:

- [ ] Touch targets: 56px minimum (recording button: 80px)
- [ ] Text contrast: 6:1 minimum against background
- [ ] `accessibilityRole`, `accessibilityLabel` on all interactive elements
- [ ] `accessibilityLiveRegion="assertive"` on error messages
- [ ] No time pressure (no countdowns, no auto-advance, no auto-dismiss)
- [ ] Family language in all user-facing copy (see Voice & Tone in CLAUDE.md)

### 10. No Placeholder Work

- [ ] If a feature needs 6 translations → all 6 are written
- [ ] If a button has an `onPress` → it does something real
- [ ] If a screen exists → it renders real content, not "TODO" or "Coming soon"
- [ ] If an error state exists → the message is warm and helpful, not generic

## Pre-Commit Sequence

Run these in order. Stop at first failure.

```bash
pnpm typecheck          # 1. Types
pnpm lint               # 2. Lint
pnpm i18n:validate      # 3. Locale completeness (if any locale files touched)
pnpm build              # 4. Full build (catches what typecheck misses)
pnpm test               # 5. Tests (if they exist for changed code)
```

Then verify manually:

- Start dev server, hit affected routes, check content
- No `console.error` in output
- No secrets in staged files (`git diff --cached | grep -i "secret\|password\|key="`)

## The "Done" Definition

A task is NOT done until:

1. All applicable checklist items above pass
2. The build is green
3. The dev server runs without errors
4. Affected routes render correctly
5. No new lint warnings introduced
6. If touching i18n: all 6 locales validated
7. If touching DB: types regenerated and RLS verified
8. If touching UI: accessibility requirements met

**If even one item fails, the task is incomplete. Say so explicitly. Do not hedge.**

## Common Rework Triggers (Learn From Our Mistakes)

These are the patterns that have burned us before:

1. **"I'll translate later"** → Ships broken UI in 5 languages → translate NOW
2. **Typecheck passes, build fails** → Always run `pnpm build`, not just typecheck
3. **Forgot RLS on new table** → Data leak → RLS on EVERY table, no exceptions
4. **`useEffect` without guard** → Double API calls, rate limits, duplicate OTP emails
5. **Hardcoded colors instead of tokens** → Inconsistent UI → import from `packages/ui/src/tokens/`
6. **`any` to make it compile** → Type safety theater → use `unknown` and narrow properly
7. **Bare `timestamp` in migration** → Timezone bugs → always `TIMESTAMPTZ`
8. **Missing `ON DELETE` on FK** → Orphaned rows or constraint errors → explicit every time

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/idmtr) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
