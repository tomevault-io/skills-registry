---
name: generate-mascot
description: Generate a character mascot for any project — a wide hero illustration plus two character-consistent variants (avatar + empty-state) — derive a favicon set from the mascot avatar, and wire them into the README and the landing page. Uses OpenAI gpt-image-1 (with /v1/images/edits for character consistency across variants) or falls back to Google Gemini Nano Banana 2. Derives the prompt from the README + 'Why <name>' rationale + any CONTEXT.md / parent PRD so the character matches the app's spine. Idempotent — re-run to regenerate. Use when the user says "generate a mascot", "give this project a mascot", "make a little character for the app", "brand this with a character", or "add a mascot to the README". Sibling of /ro:share-assets (which embeds this as its hero/mascot step) and /ro:generate-image (which is generic image-gen, not project-aware). Use when this capability is needed.
metadata:
  author: RonanCodes
---

# Generate a project mascot

Give the project a face: one wide hero illustration plus two square mascot variants of the same character. Drop them into `public/brand/`, wire them into the README and the landing/home page if one exists. The character stays consistent across all three because the variants are generated via OpenAI's `/v1/images/edits` endpoint with the hero as the reference image, plus a re-used CHARACTER REFERENCE paragraph.

**This is the standalone artist tool.** Run it on any existing project to add (or refresh) a mascot. `/ro:share-assets` calls it as its hero/mascot step; this skill can also be invoked directly.

## When to use

- User says "generate a mascot", "give the project a character", "brand this", "make a little squirrel/cat/owl for the README".
- Right after a project rename, when the old illustration no longer fits the new metaphor.
- After a feature shift that changes the app's spine (e.g. the README's "Why \<name\>" rationale changed).
- Any time `public/brand/<app>-hero.png` is missing on a project that looks shipped enough to warrant a face.

## When NOT to use

- For OG / share images — that's `/ro:share-assets`. (Favicons + app icons ARE generated here now — derived from the mascot avatar, see Step 6.5 — so the brand face and the browser-tab icon are always the same character.)
- For one-off, non-project images — that's `/ro:generate-image` (generic).
- For projects with an existing designed mascot from a human illustrator — they have one, don't overwrite.

## Usage

```
/ro:generate-mascot                        # auto-detect everything from the cwd
/ro:generate-mascot --app acorn            # override the app name (defaults to package.json name / wrangler name / dirname)
/ro:generate-mascot --hero-prompt "..."    # override the auto-derived hero prompt
/ro:generate-mascot --no-variants          # generate the hero only, skip the mascot variants
/ro:generate-mascot --no-favicon           # skip deriving the favicon + app icons from the mascot avatar (Step 6.5)
/ro:generate-mascot --no-wire              # skip the README + landing-page wiring; just produce the PNGs
/ro:generate-mascot --refresh              # regenerate even if the files already exist (otherwise: skip and report)
/ro:generate-mascot --commit               # commit the result (matches the repo's commit convention)
```

## Step 1 — Detect project + character source

Find the project root (cwd if it has `README.md` + `package.json` / `wrangler.jsonc` / `pyproject.toml`, else walk up).

App name resolution order:
1. `--app <name>` flag.
2. `package.json` `"name"`.
3. `wrangler.jsonc` / `wrangler.toml` `"name"`.
4. Repo basename.

Lowercase + kebab-case the name. This is the `<app>` slug used in file names: `public/brand/<app>-hero.png`, `<app>-mascot.png`, `<app>-mascot-asleep.png`.

## Step 2 — Derive the character + style from the project

Read, in order, and stop when there's enough signal:

1. `README.md` first paragraph + any `> **Why <name>:**` blockquote near the top + any `## What it is` section.
2. `CONTEXT.md` (Pocock domain doc).
3. `docs/specs/*.md` or `docs/agents/*.md`.
4. Parent PRD if a GitHub issue is linked from the README (`gh issue view <n>`).

From those, extract:

- **The metaphor at the centre of the name.** What noun is the app "about"? (For Acorn: an acorn. For Hummingbird: a hummingbird. For Drey: a nest.) If the name isn't a literal noun, infer from the README's "Why \<name\>" rationale.
- **The mood, in 3-5 words.** Pull from the README's tone. Calm, dreamy, playful, focused, sharp.
- **One reference product** whose feel is adjacent, only if mentioned in the README; otherwise omit.

If the README is too thin to extract these, ask the user via `AskUserQuestion` for exactly three things: the central metaphor, the mood (3 words), and one reference product. Don't interview further.

## Step 3 — Resolve the image-gen key

Order:

1. `OPENAI_API_KEY_<APP_UPPER>` (e.g. `OPENAI_API_KEY_ACORN`). The recommended per-app key convention.
2. `OPENAI_API_KEY` (the catch-all).
3. Any other `OPENAI_API_KEY_*` if the user explicitly picks one via `AskUserQuestion`.
4. If none of the above OR the OpenAI key returns `billing_hard_limit_reached`, fall back to Google Gemini via `/ro:generate-image` using `GOOGLE_AI_API_KEY`. Gemini gives a less reliable character match on variants but produces the hero fine.

Verify the key with a low-cost call (`GET /v1/models`) before spending on a generation.

## Step 4 — Write the CHARACTER REFERENCE paragraph

The single most important artefact. The paragraph that locks the character. Write it once, save it in working memory, re-use in every subsequent prompt verbatim.

Shape:

```
A small <species/build descriptor> <name'd character or unnamed>: <colour palette
of the creature>, <fur/feather/skin texture>, <distinctive feature like tail or
ears or eyes>, <expression vocabulary the character defaults to>. Same warm
painterly hand-painted illustration style: <2-3 brushwork / lighting / palette
notes that pin the visual style>.
```

Concrete example (Acorn): _"The same small plump red squirrel from the reference image: reddish-brown fur, soft white belly, fluffy bushy tail, small triangular ears, gentle round dark eyes, friendly approachable expression. Same warm painterly hand-painted illustration style as the reference: soft amber and warm tones, gentle muted shadows, slight golden glow on the acorn."_

The character reference is what carries the brand across the three (or more) variants. Drift happens when a variant prompt re-imagines the character; the reference paragraph prevents that.

## Step 5 — Generate the hero

```bash
mkdir -p public/brand
HERO_PROMPT="A warm hand-painted illustration in [palette from Step 2]: <the central metaphor, rendered concretely, with 2-3 supporting props that reinforce the app's spine>. <Mood adjectives from Step 2>. Painterly brushwork, gentle muted shadows, slight glow on the focal subject. No text, no logos, no UI elements, no people unless the persona is human. Wide composition, suitable for a website hero image."

curl -s -X POST https://api.openai.com/v1/images/generations \
  -H "Authorization: Bearer $KEY" \
  -H "Content-Type: application/json" \
  -d "$(jq -n --arg p "$HERO_PROMPT" '{model:"gpt-image-1", prompt:$p, size:"1536x1024", n:1, quality:"medium"}')" \
  | jq -r '.data[0].b64_json' | base64 -d > "public/brand/<app>-hero.png"

# compress
pngquant --quality=70-90 --output "public/brand/<app>-hero.png" --force "public/brand/<app>-hero.png" \
  || sips -s format png --resampleHeightWidthMax 1200 "public/brand/<app>-hero.png" --out "public/brand/<app>-hero.png"
```

Target file size: 400-800 KB after compression. 2.4 MB out of gpt-image-1 medium quality is normal; pngquant gets it under 1 MB cleanly.

## Step 6 — Generate the mascot variants (character-consistent, via edits endpoint)

Skip if `--no-variants`. Otherwise generate two: an avatar version and one mood variant. **Every variant prompt opens with the CHARACTER REFERENCE paragraph from Step 4 verbatim**, and uses the hero PNG as the reference `image` for the `/v1/images/edits` call.

### Variant 1: `<app>-mascot.png` (avatar)

```
<CHARACTER REFERENCE paragraph from Step 4>
A square illustration. The character sits upright facing forward,
<pose anchored to the metaphor — e.g. "holding the glowing golden acorn with
both paws close to its chest" / "perched on a small branch looking forward">,
eyes open and gentle, looking thoughtful and approachable.
No text, no logos, no other props. Soft transparent background.
Suitable for use as an app mascot avatar.
```

### Variant 2: a mood variant matching the app's spine

Choose ONE mood that matches the app's central feature:

- **"sleeping"** — for apps with a nightly job / overnight processing (e.g. Acorn's dream). Pose: curled up, eyes closed peacefully, central object nestled beside.
- **"curious"** — for research / discovery apps. Pose: paw on a small object, head tilted, alert.
- **"writing"** / **"working"** — for notes / journaling apps. Pose: paw on a tiny notebook, focused expression.
- **"thinking"** — for agent / AI apps. Pose: looking up gently, paw under chin.

Filename: `<app>-mascot-<mood>.png` (e.g. `acorn-mascot-asleep.png`).

```
<CHARACTER REFERENCE paragraph from Step 4>
A square illustration. The character is <pose for the chosen mood>.
<Any prop the mood requires, kept minimal>.
No text, no logos, no other props (no extra background details).
Soft transparent background.
Suitable for use as a small empty-state / mood illustration.
```

### Call shape for each variant

```bash
curl -s -X POST https://api.openai.com/v1/images/edits \
  -H "Authorization: Bearer $KEY" \
  -F model=gpt-image-1 \
  -F "image=@public/brand/<app>-hero.png" \
  -F size=1024x1024 \
  -F background=transparent \
  -F quality=medium \
  -F n=1 \
  -F "prompt=$VARIANT_PROMPT" \
  | jq -r '.data[0].b64_json' | base64 -d > "public/brand/<app>-mascot[-mood].png"
pngquant --quality=70-90 --output "public/brand/<app>-mascot.png" --force "public/brand/<app>-mascot.png"
```

Each variant is ~$0.04 at medium quality. Two variants over the hero = ~$0.12 per project, plus ~$0.16 for the hero. Budget ~$0.30 per mascot pass.

## Step 6.5 — Derive the favicon + app icons from the mascot avatar

**Always run this** (unless `--no-favicon`). The browser-tab icon should BE the mascot, so the brand face and the favicon never drift apart. Source is the avatar variant `public/brand/<app>-mascot.png` (the square, transparent, forward-facing one from Step 6); if `--no-variants` was passed, fall back to a centre-crop of the hero.

The mascot is a painterly raster, so the favicon set is PNG + ICO (no vector `favicon.svg` — that's a logo mark, out of scope). Generate into `public/` with ImageMagick (`magick`), falling back to `sips`:

```bash
SRC="public/brand/<app>-mascot.png"          # avatar; else hero centre-crop
BG="#ffffff"                                   # opaque bg for apple-touch (iOS ignores alpha); use the brand bg from Step 2 if there is one

# square master at 512, transparent
magick "$SRC" -background none -gravity center -resize 512x512 -extent 512x512 public/icon-512.png
magick public/icon-512.png -resize 192x192 public/icon-192.png            # PWA manifest
magick public/icon-512.png -resize 32x32   public/favicon-32x32.png
magick public/icon-512.png -resize 16x16   public/favicon-16x16.png
# apple-touch: flatten onto opaque bg (iOS squares + drops transparency)
magick public/icon-512.png -resize 180x180 -background "$BG" -flatten public/apple-touch-icon.png
# multi-resolution .ico
magick public/icon-512.png -define icon:auto-resize=16,32,48 public/favicon.ico

pngquant --quality=70-95 --force --ext .png public/icon-512.png public/icon-192.png \
  public/favicon-32x32.png public/favicon-16x16.png public/apple-touch-icon.png 2>/dev/null || true
```

`sips` fallback when ImageMagick is absent: `sips -z 512 512 "$SRC" --out public/icon-512.png` then `sips -z N N` per size; generate the `.ico` with `magick` only (sips can't write multi-res ico — if neither is available, ship `favicon-32x32.png` and skip the `.ico`, and say so).

Files written: `public/favicon.ico`, `public/favicon-16x16.png`, `public/favicon-32x32.png`, `public/apple-touch-icon.png`, `public/icon-192.png`, `public/icon-512.png`.

Head wiring (part of Step 7/8, skip with `--no-wire`): add to the app's `<head>` (TanStack Start `__root.tsx` `head()` / Next `app/layout.tsx` metadata / Astro layout):

```html
<link rel="icon" href="/favicon.ico" sizes="any">
<link rel="icon" type="image/png" sizes="32x32" href="/favicon-32x32.png">
<link rel="icon" type="image/png" sizes="16x16" href="/favicon-16x16.png">
<link rel="apple-touch-icon" href="/apple-touch-icon.png">
```

If the project has no `public/` yet (pre-scaffold), still write the files to `public/brand/`'s sibling `public/` and note that the scaffold (or the share-assets / bootstrap story) will pick them up. `/ro:share-assets` defers to these files when they already exist rather than regenerating — the mascot is the source of truth for the favicon.

## Step 7 — Wire into the README

Insert near the top, after the `# <Title>` and any `> **Why <name>:**` blockquote, before the first prose paragraph. Markdown is the format because GitHub renders it.

```markdown
<p align="center">
  <img src="public/brand/<app>-hero.png" alt="<one-sentence alt text describing the illustration>" width="640">
</p>
```

If the README already has a hero block (idempotent re-run), replace it. Detect by grepping for `public/brand/.*-hero.png` in the README.

Skip with `--no-wire`.

## Step 8 — Wire into the landing page (when one exists)

Detect a home route in this order:

1. TanStack Start: `src/routes/index.tsx`.
2. Next.js: `app/page.tsx` or `pages/index.tsx`.
3. Astro: `src/pages/index.astro`.
4. SvelteKit: `src/routes/+page.svelte`.

If a home route exists, insert a `<figure>` block above the page's main heading. Match the framework's idioms:

```tsx
// TanStack Start / React (`src/routes/index.tsx`)
<figure className="overflow-hidden rounded-xl border border-neutral-800 shadow-lg">
  <img
    src="/brand/<app>-hero.png"
    alt="<same alt text as the README>"
    width={1200}
    height={800}
    className="h-auto w-full"
  />
</figure>
```

If the home route is auth-gated (the app sits behind Cloudflare Access or a login) and shows the hero only post-auth, that's fine. The hero is a brand asset, not a marketing one.

If the home route already references a `*-hero.png` in `public/brand/`, replace; otherwise insert. Idempotent.

**One mascot per surface — do NOT stack the hero and the avatar on the same screen.** If this surface already shows the hero (above), keep its wordmark text-only; do NOT also drop the small avatar next to it. Two of the same character on one screen reads as clutter and cheapens the painted hero. (Lesson from Settle + the ADHD helper, where the hero plus a 32px avatar by the wordmark stacked two capybaras on the sign-in page.)

The small `<app>-mascot.png` avatar belongs on surfaces that do NOT show the hero: the persistent app header / nav kicker on **inner (post-auth) pages**. If such an inner header exists (and it isn't the sacred primary working screen), replace its text-only kicker with a `<div>` containing the mascot at 32-48px alongside the name. If the only branded surface is the one already showing the hero, skip the avatar entirely.

Skip all wiring with `--no-wire`.

## Step 9 — Commit (optional)

With `--commit`, stage `public/brand/*` and the touched README + home route file, and commit with the repo's commit convention (`✨ feat:` / `📝 docs:` per repo CLAUDE.md). Respect the weekday-hours timestamp rule from `~/CLAUDE.md`. Do NOT push automatically — leave that to the user or the next ship skill.

Without `--commit`, leave the working tree dirty and report the files written so the user can review before committing.

## Audit-mode

`/ro:generate-mascot --audit-only` reports which files are present and whether the README + home page reference them. Exit 0 if all present and wired; 1 if missing.

## What this skill does NOT do

- Design a vector logo / brand mark — a single-colour square SVG. The favicon here is the painterly mascot rasterised down (Step 6.5), not a vector mark. If you want a clean vector logo, delegate to `/ro:generate-image` or a human illustrator.
- Generate OG / share images — that's `/ro:share-assets` (which calls this skill for the hero/mascot part, and now defers to the mascot-derived favicon set rather than making its own).
- Animate the mascot — out of scope; if you want motion, export the mascot and animate in Rive / Lottie / Framer Motion.

## Per-app OpenAI key convention

Prefer `OPENAI_API_KEY_<APP_UPPER>` so spend stays scoped to the project. A billing-limit hit on one app doesn't block the others, and rotation is per-app. If a per-app key isn't set, the global `OPENAI_API_KEY` catches all. Pair with `/ro:env` to add a per-app key cleanly.

## Related

- `/ro:share-assets` — wraps this skill as its hero/mascot step; generates the OG image, manifest, head meta. Favicons now come from THIS skill (Step 6.5, mascot-derived); share-assets defers to them when present.
- `/ro:generate-image` — generic image gen (Gemini Nano Banana 2); used as the fallback when no OpenAI key is available.
- `/ro:env` — adds API keys to `~/.claude/.env` without them passing through chat.
- `/ro:share-assets` § Step 3 — the embedded copy of these instructions, kept in sync.

## Provenance

- **2026-06-07** — created after the Acorn rename. The hero illustration was generated first (gpt-image-1, the sleeping-squirrel-and-acorn scene); two mascot variants followed using the hero as the reference image. Character held cleanly across all three. Extracted from `/ro:share-assets` so it can be invoked on any existing project, not only during a from-scratch scaffold.
- **2026-06-07** — added Step 6.5: derive the favicon + app-icon set from the mascot avatar so the browser-tab icon IS the mascot and never drifts from the brand face. Favicon ownership moved from `/ro:share-assets` to here (share-assets now defers). Per Ronan: "whenever we generate a new mascot, use that generated version as the favicon too." Added `--no-favicon`.

---
> Source: [RonanCodes/ronan-skills](https://github.com/RonanCodes/ronan-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
