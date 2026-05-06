---
name: rsshub-route-developer
description: > Use when this capability is needed.
metadata:
  author: neversight
---

# RSSHub Route Developer

Develop RSSHub routes that turn any website into an RSS feed. Follow the six phases below in order.

## Phase 1: Environment Setup

Check if an RSSHub repository clone exists in the working directory or a nearby path.

**If no clone exists:**

```bash
git clone https://github.com/DIYgod/RSSHub.git && cd RSSHub && pnpm install
```

**If clone exists:** verify `node_modules/` exists. Run `pnpm install` if not.

Requirements: Node.js >= 22, pnpm (install via `npm install -g pnpm` if missing).

## Phase 2: Analyze Target Page

1. Fetch the page with `curl -s <url>` or browser tools and inspect the HTML source
2. Determine rendering type:
   - **SSR check:** compare `curl` output vs browser-rendered DOM — if `curl` contains article data, it's SSR → use `got` + `cheerio`
   - **CSR:** data loaded via JS — inspect browser network requests for JSON API endpoints; use Puppeteer only as last resort
3. Identify CSS selectors for: list container, title, link, date/time, author, image
4. Check detail pages — if list page lacks full content, plan per-article fetches
5. Note the date format for `parseDate` compatibility

## Phase 3: Generate Route Code

Create three files under `lib/routes/{namespace}/`. Read `references/route-templates.md` for complete templates, type definitions, and common patterns.

```
lib/routes/{namespace}/
├── namespace.ts      # Site metadata (use secondary domain as namespace, all lowercase)
├── {route-name}.ts   # Route handler
└── radar.ts          # Browser extension discovery rules
```

### Handler workflow

1. Build URL from route parameters
2. Fetch list page with `got`, parse with `cheerio`
3. Extract items (title, link, pubDate, author)
4. Fetch full article content per item via `cache.tryGet`
5. Parse dates with `parseDate` from `@/utils/parse-date`
6. Return `{ title, link, description, image, item }`

### RSSHub-specific conventions

- Import `got` from `@/utils/got`, `cache` from `@/utils/cache` (not from npm directly)
- Access route params via Hono: `ctx.req.param('name')`, `ctx.req.query('name')`
- Set all `features` flags to `false` unless specifically needed
- Valid categories: `popular`, `social-media`, `new-media`, `traditional-media`, `bbs`, `blog`, `programming`, `design`, `live`, `multimedia`, `picture`, `anime`, `program-update`, `university`, `forecast`, `travel`, `shopping`, `game`, `reading`, `government`, `study`, `journal`, `finance`, `other`

## Phase 4: Local Testing

1. Start dev server: `pnpm run dev` (port 1200)
2. Test: `curl http://localhost:1200/{namespace}/{route-path}`
3. Verify: items exist, descriptions are non-empty, dates parse correctly

**Common issues:**
- Empty descriptions → wrong selector for article body; inspect actual detail page HTML
- Stale cache after fix → restart dev server
- Port 1200 in use → kill existing process
- No items → site may be CSR (check view-source vs rendered DOM)

## Phase 5: Code Quality

```bash
pnpm run format
pnpm run lint
```

**Warning:** `pnpm run format` reformats files **across the entire repo**, not just your route files. After running, **immediately** discard unrelated changes with `git checkout -- .`, then stage only your route files with `git add lib/routes/{namespace}/`.

## Phase 6: Submit PR

Read `references/pr-submission.md` for the complete PR workflow: fork/branch strategy, commit format, mandatory PR template, and checklist guidance.

**Critical:** the PR body must include a `` ```routes `` fenced code block with route paths. PRs missing this are automatically closed.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
