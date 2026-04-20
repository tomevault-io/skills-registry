---
name: validate
description: Run automated tests and generate a manual validation checklist after completing a work phase Use when this capability is needed.
metadata:
  author: jasonlemming
---

# Phase Validation

You have just completed a work phase. Run automated validation, then generate a manual validation checklist tailored to the changes made.

The argument passed to this skill (if any) describes what was just built or changed. If no argument is provided, infer the scope from recent git changes (`git diff --stat HEAD~1` or `git diff --staged --stat`).

---

## Step 1: Automated Validation

Run the test suite FIRST. Always present test results before the manual checklist.

```
pytest tests/ -v --tb=short
```

Report results clearly:
- Total passed / failed / skipped
- List any failures with file and test name
- If all pass, a single "All N tests passed" line is sufficient

If tests fail, flag them prominently and note whether the failures are related to the current changes or pre-existing.

---

## Step 2: Determine Scope

Before generating the checklist, assess what changed:
- Which files were modified? (`git diff --stat` against the starting point)
- Which systems are affected? (Vercel public pages, Railway API, admin UI, database, email)
- How large is the change? (single fix vs. multi-file feature)

**Scale the checklist proportionally:**
- Small fix (1-3 files, single behavior): 3-5 numbered items
- Medium feature (4-10 files, multiple behaviors): 6-12 items grouped by area
- Large feature (10+ files, new system): 10-20+ items organized into lettered sections (A, B, C...) with numbered sub-items (A1, A2...)

---

## Step 3: Generate Manual Validation Checklist

**IMPORTANT**: Only generate production URL checklists *after* the code has been committed and pushed. A checklist against production URLs is meaningless if the deploy hasn't happened yet. If the code isn't deployed, say so and offer to generate the checklist after push.

### Format Rules (NON-NEGOTIABLE)

These rules come directly from what the user has explicitly requested and responded well to across dozens of sessions:

1. **Every URL must be a full, copy-pasteable production URL.** Not `/hearings` but `https://www.capitollabsllc.com/hearings`. Not `/api/hearings` on Railway but `https://steadfast-tranquility-production.up.railway.app/api/hearings`. The user copy-pastes these directly into a browser.

2. **Every item gets a code** (A1, B3, etc. for large changes; just 1, 2, 3 for small ones). The user references these codes in feedback: "D1 - pass; D2 pass but still seeing the darkened dividers..."

3. **Expected results must be visual and specific.** Not "page should load correctly" but "green badge with text 'Transcript Available' appears to the right of the hearing title" or "URL updates to include `?chamber=Senate`, only Senate committees are listed."

4. **Include the exact text, color, or state** the user should see where applicable. Quote expected strings: *"Toast says 'Transcript requested!'"*

5. **Use a table format** for each section:

```
| # | Test | Expected Result | Status |
|---|------|-----------------|--------|
| A1 | Visit [/hearings](https://www.capitollabsllc.com/hearings) | 200, hearing list with filter sidebar visible | NEW |
```

6. **Status column values:**
   - `NEW` — first time testing this item
   - `PASSED` — confirmed working in a previous round (keep visible as regression watchlist)
   - `RETEST` — was broken, fix applied, needs re-verification
   - `TEST` — existing feature that should be spot-checked for regressions

7. **Deploy prerequisites** go at the top, before the checklist. Examples:
   - "These tests require the Vercel deploy from commit `abc123` to be live"
   - "Migration `postgres_015` must be applied before testing Section C"
   - "Railway must redeploy for API changes to take effect"

### Checklist Structure

Use this structure, including or omitting sections based on scope:

#### Deploy Prerequisites (if applicable)
What must be deployed/applied before testing begins.

#### New Feature Tests
The primary items being validated. One section per functional area for large changes.

#### Regression Spot-Checks
Quick checks on adjacent features that could have been affected. Keep these brief — one line per check with a URL and what to verify.

#### Troubleshooting Guidance (if applicable)
For new integrations or complex features, include "If X doesn't work, check Y" guidance. Examples:
- "If transcript preview shows 'No transcript available', verify the Capitol Transcribe API is reachable from Vercel (`curl https://steadfast-tranquility-production.up.railway.app/health`)"
- "If the admin page shows a blank white screen, check browser console for 404 on `main.b3b7498e.js`"

#### Database Verification (if applicable)
SQL queries the user can run to confirm backend state:
```sql
SELECT count(*) FROM email_preferences WHERE week_ahead_enabled = true;
```

---

## Step 4: Iterative Rounds

If the user reports back with per-item results (e.g., "A1 pass, A2 fail — the badge color is wrong"), update the checklist:
- Mark passing items as `PASSED`
- Mark items with issues as `RETEST` after fixing
- Add `CONTEXT` blocks for retests explaining what broke and how it was fixed:

```
### Section A: Committee Filters [RETEST]

**Context**: Filter selection wasn't updating the query string. Root cause: custom select JS was hiding native `<select>` change events. Fixed by binding to the custom dropdown's click handler instead.
```

- Keep `PASSED` items visible (they serve as a regression watchlist)
- Add a **priority note** at the bottom: "Start from A3 (first RETEST item)"

---

## Reference: Common Validation URLs

### Public Site (Vercel)
- Homepage: `https://www.capitollabsllc.com/`
- Hearings list: `https://www.capitollabsllc.com/hearings`
- Hearing detail (example with transcript): use a real URL slug from the current changes
- Committees: `https://www.capitollabsllc.com/committees`
- Committee detail: `https://www.capitollabsllc.com/committee/<sys_code>`
- Members: `https://www.capitollabsllc.com/members`
- Member detail: `https://www.capitollabsllc.com/member/<bioguide_id>`
- Witnesses: `https://www.capitollabsllc.com/witnesses`
- Search: `https://www.capitollabsllc.com/search?q=<term>`
- Sitemap: `https://www.capitollabsllc.com/sitemap.xml`

### Admin (Vercel, auth required)
- Dashboard: `https://www.capitollabsllc.com/admin/`
- Users: `https://www.capitollabsllc.com/admin/users`
- Feedback: `https://www.capitollabsllc.com/admin/feedback`
- Analytics: `https://www.capitollabsllc.com/admin/analytics`
- Capitol Transcribe UI: `https://www.capitollabsllc.com/admin/capitoltranscribe`

### Transcription API (Railway)
- Health: `https://steadfast-tranquility-production.up.railway.app/health`
- Hearings: `https://steadfast-tranquility-production.up.railway.app/api/hearings/`
- Published: `https://steadfast-tranquility-production.up.railway.app/api/hearings/?is_published=true`

### Auth Pages
- Login: `https://www.capitollabsllc.com/login`
- Signup: `https://www.capitollabsllc.com/signup`

### Email System
- Dry run Week Ahead: `https://www.capitollabsllc.com/api/cron/content-email?type=week_ahead&dry_run=1`
- Dry run Week Review: `https://www.capitollabsllc.com/api/cron/content-email?type=week_review&dry_run=1`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jasonlemming) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
