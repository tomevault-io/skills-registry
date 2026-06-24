---
name: seo-engine
description: Use when generating SEO + AI-SEO content (programmatic pages, schema.org markup, llms.txt manifests, sitemap) for the current product. Builds the full 5-pillar architecture (/blog, /vs, /alternatives, /integrations, /glossary), performs free keyword research (Google autosuggest + PAA + Reddit/Quora + competitor teardown), and produces a codebase-specific WIRE-UP.md with framework-correct edits. Optimized for both classic Google search and AI-powered search (ChatGPT, Perplexity, Claude, Gemini).
metadata:
  author: sofyanjamil
---

# seo-engine

5-pillar SEO + AI-SEO content generator. Produces `./growth/03-seo/`.

## When to invoke

- User asks: "generate SEO content", "build programmatic SEO", "add a blog/glossary/comparison pages".
- Invoked by `/growth` orchestrator as phase 3 of 3.
- Standalone via `/growth-seo`.

## Prerequisites

Requires:
- `./growth/references/icp.md` — for audience-aligned keyword targeting.
- `./growth/references/positioning.md` — for product-led content angles.
- `./growth/references/competitors.md` — for `/vs` and `/alternatives` pages.

If any are missing, ABORT and prompt user to run `/growth-market` first.

## Three sub-phases

### Phase A: Keyword research (free signals only)

Output: `./growth/references/keywords.md`

Multi-source pipeline. NO paid APIs.

1. **Seed generation**: From ICP + positioning, LLM produces ~20 seed keywords across 5 intents (informational, commercial, transactional, navigational, comparative).

2. **Google autosuggest expansion**:
   - `WebFetch https://suggestqueries.google.com/complete/search?client=firefox&q=<seed>` for each seed.
   - Parse JSON response, extract suggestions.
   - Also `WebFetch https://www.google.com/search?q=<seed>` for the PAA box and "Related searches" section at bottom.

3. **Reddit / Quora question mining**:
   - `WebSearch site:reddit.com "<problem space>"` — surface top threads.
   - `WebSearch site:quora.com "<problem space>"`.
   - `WebFetch` 2-3 top threads → extract titles + top comment questions.

4. **Competitor teardown**:
   - For each competitor URL in `competitors.md`:
     - `WebFetch <competitor>/sitemap.xml` if exposed.
     - `WebFetch <competitor>/blog` to extract their indexed blog titles.
     - `WebSearch site:<competitor-domain>` to discover their indexed pages.
   - Map each competitor URL to inferred target keyword (from title + H1).

5. **LLM consolidation**:
   - Cluster all discovered keywords into 5 buckets (one per pillar).
   - Tag each with: intent, estimated_volume (low/medium/high based on autosuggest depth + PAA presence), competition (qualitative), target_persona.
   - Write to `keywords.md` as a structured markdown table per pillar.

### Phase B: Page generation (strategy here on Opus, writing dispatched to Sonnet agents)

Output: `./growth/03-seo/pages/<pillar>/<slug>.md` and `.html`

Page volume per `--depth` flag:

| Flag | /blog | /vs | /alternatives | /integrations | /glossary | TOTAL |
|---|---|---|---|---|---|---|
| `--depth lean` (default for dry runs) | 3 | 2 | 2 | 2 | 5 | 14 |
| `--depth standard` (DEFAULT) | 5 | 5 | 5 | 5 | 10 | 30 |
| `--depth deep` | 15 | 10 | 10 | 10 | 60 | 105 |

**Two-tier model split** — strategy stays in this skill (parent model, typically Opus); body writing dispatches to the `seo-page-writer` agent (Sonnet, cost-efficient for prose).

**B.1 Strategy (this skill, Opus by inheritance)** — for each page across all pillars:
1. Pick top-scoring keyword from the pillar bucket in `keywords.md`.
2. Identify target persona, search intent, target word count (per pillar).
3. For `/vs` and `/alternatives`: bind to a specific competitor from `competitors.md`.
4. For `/glossary`: extract the canonical term.
5. Author an `og_image_description` (model-agnostic — the agent doesn't write images, it only references this in frontmatter).
6. Resolve internal-link slugs (which other pages in this run does this page link to? — anchor the linking graph globally).
7. Assemble a complete **page brief** per `${CLAUDE_PLUGIN_ROOT}/agents/seo-page-writer.md` "Your inputs" spec.

Group page briefs by pillar.

**B.2 Writing (5 parallel `seo-page-writer` agents, Sonnet)** — spawn one agent per pillar (5 total, in parallel via a single message with 5 Agent calls):
- Each agent receives all briefs for its pillar.
- Each agent reads the shared `references/` lib (same as this skill) and the plugin's `seo-patterns.md` + `ai-seo-tactics.md` + `output-schemas.md`.
- Each agent writes `<slug>.md` + `<slug>.html` for every page in its pillar — applying the pillar template, embedding schema.org, injecting citation magnets, writing semantic HTML preview.
- Each agent returns a SHORT summary (pillar, pages completed, avg word count, flags).

**B.3 Post-fanout review (this skill, Opus)**:
1. Read every generated `.md` frontmatter to verify schema completeness.
2. Verify internal-link graph: every `internal_links[]` entry actually points to a page that was generated. If a link is dangling (e.g. promised a glossary slug that didn't make it into Phase B), either regenerate that glossary entry or rewrite the source page's link.
3. Verify pillar-specific structural rules held (e.g. /vs pages have a comparison table, /glossary pages have a definition-first sentence).
4. If any page is structurally broken, dispatch a single follow-up Sonnet agent call to regenerate just that page.

### Phase C: Site-wide artifacts + wire-up

Output: `./growth/03-seo/llms.txt`, `llms-full.txt`, `sitemap.xml`, `robots.txt` (suggested), `WIRE-UP.md`.

1. **llms.txt** — index of top ~30 most important pages per `ai-seo-tactics.md` §1 format.
2. **llms-full.txt** — every generated page.
3. **sitemap.xml** — every generated page with priority hints per `ai-seo-tactics.md` §6.
4. **robots.txt** — suggested content, explicitly allows `GPTBot`, `ClaudeBot`, `Perplexity-User`, `OAI-SearchBot`, `Google-Extended`.

5. **WIRE-UP.md** — codebase-specific implementation plan. Steps:
   - **Detect framework** by reading `package.json`:
     - `next` → Next.js (note App Router vs Pages Router by checking dir).
     - `astro` → Astro.
     - `@sveltejs/kit` → SvelteKit.
     - `vue` + `@vitejs/plugin-vue` → Vue.
     - Otherwise → plain HTML / static.
   - **Detect styling**: tailwind config? CSS modules? styled-components?
   - **Emit framework-correct snippets** for each pillar:
     - Next.js App Router: `src/app/<pillar>/[slug]/page.tsx` + content dir + `generateStaticParams`.
     - Astro: `src/content/<pillar>/<slug>.md` with content collection schema.
     - SvelteKit: `src/routes/<pillar>/[slug]/+page.svelte` + `+page.server.ts`.
   - **Idempotency markers**: each generated edit includes a comment marker like `// @growth-engine:wire-up:<edit-id>` so re-running detects already-applied edits.

## Outputs

```
./growth/03-seo/
├── pages/
│   ├── blog/
│   │   ├── <slug>.md   (frontmatter + body)
│   │   └── <slug>.html (rendered preview, semantic HTML)
│   ├── vs/
│   ├── alternatives/
│   ├── integrations/
│   └── glossary/
├── llms.txt
├── llms-full.txt
├── sitemap.xml
├── robots.txt
└── WIRE-UP.md
```

Plus update to:
- `./growth/references/keywords.md`

## Codebase auto-wire prompt

After generation, the skill returns control. The `/growth` orchestrator (or standalone `/growth-seo`) then asks:

> "SEO generation complete. 30 pages at `./growth/03-seo/`. Would you like to wire these into your codebase now? (yes / preview / skip)"

If yes/preview → invoke `/growth-wire` slash command which reads `WIRE-UP.md` and applies edits interactively with per-edit confirmation.

The `seo-engine` skill itself NEVER writes to source code. All codebase writes go through `growth-wire`.

## Flags

- `--depth lean|standard|deep` (default `standard`)
- `--pillars blog,vs,alternatives,integrations,glossary` — comma list to restrict.
- `--no-llms-txt` — skip llms.txt generation (rarely useful).
- `--lang en` — content language. SEO content is currently single-language per run; for multi-lang, run the skill once per language with different output dirs (consider hreflang as future extension).

## Verification

After running:
- [ ] All requested pages exist as `.md` + `.html` pairs.
- [ ] Every `.md` frontmatter validates against `output-schemas.md` §4.
- [ ] Every page has at least 1 schema.org block + 1 citation magnet + 3 internal links.
- [ ] `llms.txt` and `sitemap.xml` reference every generated page.
- [ ] `WIRE-UP.md` correctly identifies the detected framework.
- [ ] Spot-check 1 page per pillar: H1 is entity-first, FAQ block has 4+ entity-rich Q&As, internal links resolve to other generated pages.

---
> Source: [sofyanjamil/growth-engine](https://github.com/sofyanjamil/growth-engine) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
