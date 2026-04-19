---
name: setup-project
description: Set up the ai-starter project for local development. Guides the user through installing dependencies, configuring environment variables, and running the dev server. Use when this capability is needed.
metadata:
  author: kreas
---

# Set Up ai-starter for Local Development

Walk the user through each step below. **Ask for their credentials at each step** — do not skip ahead or assume values.

## Step 1: Install dependencies

```bash
pnpm install
```

## Step 2: Create `.env.local` from template

Check if `.env.local` already exists. If not, copy from the example:

```bash
cp .env.example .env.local
```

Then walk the user through filling in each section, one at a time.

### 2a: Turso Database

Ask the user for their Turso database URL and auth token. They can create a database at https://turso.tech.

Set these values in `.env.local`:

- `TURSO_DATABASE_URL` — e.g. `libsql://my-db-username.turso.io`
- `TURSO_AUTH_TOKEN` — their database auth token

### 2b: Cloudflare R2

Ask the user for their R2 credentials. They can set up R2 at https://dash.cloudflare.com (R2 Object Storage).

Set these values in `.env.local`:

- `R2_ACCOUNT_ID` — their Cloudflare account ID
- `R2_ACCESS_KEY_ID` — R2 API token access key
- `R2_SECRET_ACCESS_KEY` — R2 API token secret key
- `R2_BUCKET_NAME` — the name of their R2 bucket

### 2c: WorkOS AuthKit

Ask the user for their WorkOS credentials. They can create a project at https://workos.com.

Set these values in `.env.local`:

- `WORKOS_CLIENT_ID` — starts with `client_`
- `WORKOS_API_KEY` — starts with `sk_test_` or `sk_live_`
- `WORKOS_COOKIE_PASSWORD` — generate one automatically:

```bash
openssl rand -base64 24
```

- `NEXT_PUBLIC_WORKOS_REDIRECT_URI` — default is `http://localhost:3000/callback`. Ask the user if they need a different value.

**Important:** Remind the user to add `http://localhost:3000/callback` as an allowed redirect URI in their WorkOS dashboard.

### 2d: Anthropic

Ask the user for their Anthropic API key. They can get one at https://console.anthropic.com/.

Set this value in `.env.local`:

- `ANTHROPIC_API_KEY` — starts with `sk-ant-`

## Step 3: Push database schema

```bash
pnpm db:push
```

This pushes the Drizzle schema (users table) to the Turso database.

## Step 4: Start the dev server

```bash
pnpm dev
```

Confirm the app starts at http://localhost:3000 without errors.

## Step 5: Verify

Tell the user to open http://localhost:3000 in their browser. They should see:

- The "AI Starter" heading
- "Sign In" and "Sign Up" buttons (if not authenticated)
- No console errors

If there are errors, help the user debug them.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kreas) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
