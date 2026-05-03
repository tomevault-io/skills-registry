---
name: deploy-check
description: Verify that a web deployment is live and healthy after deploy. Use when deploying to Vercel, Cloudflare Pages, or any hosting provider. Checks HTTP status codes, route availability, SSL certificates, DNS resolution, response times, and critical page content. Use after any deploy, when debugging production issues, or when a user reports a site is down. Do NOT use for local development servers or pre-deploy testing. Use when this capability is needed.
metadata:
  author: philipkarns
---

# Deploy Check

Verify production deployments are live, routes resolve, and nothing is broken.

## Quick Start

Run the deploy check script against a domain:

```bash
bash scripts/deploy-check.sh <domain> [routes-file]
```

- `<domain>` — the production domain (e.g., `clawdefend.com`, `lawnlens.com`)
- `[routes-file]` — optional file with one route per line to check (defaults to common routes)

## What It Checks

1. **DNS resolution** — domain resolves to an IP
2. **SSL certificate** — valid, not expired, correct domain
3. **HTTP status codes** — each route returns expected status (200, 301, etc.)
4. **Response time** — flags routes slower than 3s
5. **Critical content** — checks for expected text/elements on key pages
6. **Redirect chains** — follows and reports redirect hops
7. **Security headers** — checks for X-Frame-Options, CSP, HSTS

## Routes File Format

One route per line. Optional expected status code and content check:

```
/ 200 "Sign up"
/login 200 "Log in"
/dashboard 302
/api/health 200 "ok"
/pricing 200 "Pro"
/nonexistent 404
```

If no routes file is provided, checks: `/`, `/login`, `/signup`, `/dashboard`, `/pricing`

## Output

Prints a summary table:

```
✅ DNS: clawdefend.com → 76.76.21.21
✅ SSL: Valid, expires 2026-05-15
Route           Status  Time    Content   Result
/               200     0.4s    ✅        ✅
/login          200     0.3s    ✅        ✅
/dashboard      302     0.2s    —         ✅
/api/health     200     0.1s    ✅        ✅
/pricing        200     0.5s    ✅        ✅
❌ /signup      500     0.8s    —         FAIL

Security Headers:
✅ X-Frame-Options: SAMEORIGIN
⚠️  Content-Security-Policy: missing
✅ Strict-Transport-Security: max-age=31536000
```

## Per-Project Route Files

Project-specific route files live in the `routes/` directory:

```
routes/
  clawdefend.routes
  lawnlens.routes
  resumemode.routes
```

Use them by passing the route file as the second argument:

```bash
bash scripts/deploy-check.sh clawdefend.com routes/clawdefend.routes
bash scripts/deploy-check.sh lawnlens.com routes/lawnlens.routes
bash scripts/deploy-check.sh resumemode.com routes/resumemode.routes
```

Each file defines the exact routes and expected content for that project, so checks are accurate and repeatable.

## When to Run

- After every Vercel/Cloudflare deploy
- When a user reports something is broken
- During product launch (run 2-3 times over first hour)
- As part of the product-launch skill checklist (when built)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/philipkarns) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
