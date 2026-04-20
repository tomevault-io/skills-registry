---
name: verify-site
description: End-to-end verification of the portfolio site. This skill should be used after making any code changes to verify the site works correctly before declaring work complete. Checks lint, build, TypeScript compilation, route accessibility, content rendering, i18n for EN and FR, hreflang tags, and Sanity Studio isolation. Invoke with /verify-site or use proactively after finishing a task. Use when this capability is needed.
metadata:
  author: tipyaf
---

# Verify Site

## Overview

Rigorous end-to-end verification of the portfolio site. Run this after every significant change
to ensure nothing is broken before declaring work complete. Never claim a task is done without
running this verification first.

## Verification Workflow

### Step 1: Clear Cache (if config changed)

If `next.config.mjs`, `middleware.ts`, `i18n/` files, or `tsconfig.json` were modified, clear
the Next.js cache first to avoid stale module errors:

```bash
rm -rf .next
```

### Step 2: Ensure Dev Server is Running

Check if a dev server is already running. If not, start one in the background:

```bash
# Check if port 3000 is in use
lsof -ti:3000 >/dev/null 2>&1 || npm run dev &
```

Wait for the server to be ready before proceeding (check for "Ready" in output or curl the base URL).

### Step 3: Run the Verification Script

Execute the bundled verification script:

```bash
.claude/skills/verify-site/scripts/verify-site.sh --project-dir "$(pwd)" --port 3000
```

Options:
- `--port PORT` — Dev server port (default: 3000)
- `--skip-build` — Skip the production build check (faster iteration)
- `--skip-lint` — Skip lint check
- `--project-dir PATH` — Project root directory (required)

### Step 4: Browser Verification (if visual changes)

For UI/visual changes, additionally use Playwright to:

1. Navigate to `http://localhost:3000/`
2. Take a screenshot and verify content renders
3. Navigate to `http://localhost:3000/fr` and verify FR content
4. Check browser console for errors using `browser_console_messages`
5. Navigate to `http://localhost:3000/studio` and verify Sanity Studio loads

### Step 5: Update README.md

After any significant change (new feature, architecture change, new dependency, new route, config change),
check if `README.md` needs updating. Sections to review:

- **Tech Stack** — New dependency added?
- **Features** — New capability?
- **Architecture / i18n** — Routing, data flow, or structure changed?
- **Project Structure** — New directories or files?
- **Commands** — New script in package.json?
- **Environment Variables** — New env var required?

If any section is outdated, update it before declaring work complete.

### Step 6: Report Results

After running all checks, report a clear summary:
- Total passed / failed
- Any failed checks with details
- If failures exist, investigate and fix before marking work as complete

## Checks Performed

The script verifies:

| Check | What it validates |
|-------|-------------------|
| **Lint** | `npm run lint` passes with no errors |
| **Build** | `npm run build` succeeds |
| **TypeScript** | `npx tsc --noEmit` has no type errors |
| **GET /** | Default locale returns 200 |
| **GET /fr** | French locale returns 200 |
| **GET /en** | Redirects to / (307) since EN is default |
| **GET /studio** | Sanity Studio returns 200 |
| **Content size** | Both EN and FR pages have >5KB content |
| **EN ≠ FR** | EN and FR pages have different content |
| **hreflang** | Homepage includes hreflang tags for en and fr |
| **Studio isolation** | Studio page has no next-intl references |

## When to Add New Checks

Extend the verification script when:
- A new route is added (add a `check_route` call)
- A new language is added (add locale-specific checks)
- A new integration is added that could break silently (e.g., analytics, structured data)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tipyaf) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
