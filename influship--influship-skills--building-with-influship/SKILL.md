---
name: building-with-influship
description: >- Use when this capability is needed.
metadata:
  author: Influship
---

# Building with Influship

Influship is a creator-discovery API: semantic search, match scoring, and lookalike
discovery across Instagram and YouTube creators, billed by credits. This skill helps you
write correct integration code against the **TypeScript SDK** (npm `influship`) — and the
**raw REST API** when you are not on TypeScript.

## Core principles (read first)

1. **Don't trust training data — verify against the installed package and live docs.** The
   API and SDK evolve. For the version a developer actually has installed, the source of
   truth is `node_modules/influship/api.md` and the bundled `.d.ts` type definitions —
   these are never stale relative to their code. **Never guess SDK bindings** (method names,
   params, response fields); if unsure, read the installed types or fetch a live source.
   See `references/live-sources.md`.
2. **Default to the latest SDK version.** Don't pin an old version in examples unless the
   user asks.
3. **Server-side only.** The SDK runs on Node 20+, Deno, Bun, Cloudflare Workers, and Vercel
   Edge — **not** browsers or React Native (your API key must never ship to a client).
4. **Be credit-aware.** Calls cost credits. Bound `limit`, cache profile lookups, and don't
   over-paginate. See `references/credits-and-cost.md`.

## Get set up

1. Get an API key from the developer dashboard: **https://developers.influship.com**
2. Install the SDK:
   ```sh
   npm install influship
   ```
3. Set `INFLUSHIP_API_KEY` in your environment (e.g. `.env`, never committed).
4. Instantiate the client (server-side):
   ```ts
   import Influship from 'influship';

   const client = new Influship({
     apiKey: process.env['INFLUSHIP_API_KEY'], // default; can be omitted
   });

   const health = await client.health.check(); // { ok: true, timestamp: ... }
   ```

Useful env vars: `INFLUSHIP_API_KEY`, `INFLUSHIP_BASE_URL` (override base URL),
`INFLUSHIP_LOG` (`debug|info|warn|error|off`).

## Operations cheat-sheet (cached: 2026-05-29 — verify params against installed types)

| Call | Use it when |
|------|-------------|
| `client.search.create({ query, limit?, platforms?, creator_kinds?, filters? })` | **Discovery by intent** — natural-language creator search. Returns `search_id` + first page. |
| `client.search.retrieve(id, { cursor?, limit? })` | Page through the results of a prior search (paginated). |
| `client.creators.match({ creators, intent })` | **Score creators you already have** against an intent → `good`/`neutral`/`avoid` + reasons. |
| `client.creators.lookalike({ seeds, filters?, limit? })` | **Expand** from seed creators to similar ones (paginated, async-iterable). |
| `client.creators.autocomplete({ q, limit?, platform?, scope? })` | Resolve a partial name/handle to creator(s). |
| `client.creators.retrieve(id, { include? })` | Full creator profile (ai_summary, themes, brand_alignment, key_facts; `include: ['profiles']`). |
| `client.profiles.get(username, { platform })` | Cached per-platform metrics for one handle. |
| `client.profiles.lookup({ ... })` | Batch profile lookup (see installed types for exact params). |
| `client.posts.list({ ... })` | List posts (paginated; see installed types for exact params). |
| `client.raw.instagram.getProfile(username, {...})` | Live (uncached) Instagram profile scrape. |
| `client.raw.youtube.getChannel / getChannelTranscripts / getTranscript / search` | Live YouTube channel, transcripts, and search. |
| `client.health.check()` | Liveness check (no credits). |

**Which endpoint when (the #1 confusion):**
- Have an *intent* but no creators yet → `search.create`.
- Have a *list of creators* and want to know if they fit → `creators.match`.
- Have a few *great* creators and want *more like them* → `creators.lookalike`.
- Have a *handle/name* and need the canonical creator/profile → `autocomplete` then `creators.retrieve` / `profiles.get`.

Exact, current params and response shapes live in `references/operations.md` (and ultimately
in the installed types / OpenAPI — see `references/live-sources.md`).

## Best-practice pitfalls

- **Pagination:** list methods are auto-paginating with `for await … of`. Don't hand-roll
  cursors. See `references/pagination.md`.
- **Errors:** catch `Influship.APIError` subclasses; handle `RateLimitError` (429) and
  `AuthenticationError` (401) explicitly. Retries (2×, backoff) and timeouts (60s) are
  built in and configurable. See `references/errors-and-reliability.md`.
- **Credits:** see `references/credits-and-cost.md` before writing loops.
- **Non-TypeScript:** call the REST API directly — see `references/rest-api.md`.

## Reading guide

- Building a real flow end-to-end → `references/recipes.md`
- Exact method params / response fields → `references/operations.md`
- Paginating large result sets → `references/pagination.md`
- Error handling / retries / rate limits → `references/errors-and-reliability.md`
- Cost control → `references/credits-and-cost.md`
- curl / fetch / Python → `references/rest-api.md`
- Anything that might be out of date → `references/live-sources.md`

## Boundary

This skill is for **writing integration code**. To let an agent *call* the Influship API
live during a session, use the Influship MCP server (a separate skill / setup); this skill
does not depend on the MCP being connected.

## Verify your work

Before declaring an integration done, run a real call (start with `client.health.check()`,
then the actual endpoint with a tiny `limit`) and confirm the response shape matches what
your code expects.

---
> Source: [Influship/influship-skills](https://github.com/Influship/influship-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
