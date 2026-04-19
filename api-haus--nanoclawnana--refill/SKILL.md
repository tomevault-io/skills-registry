---
name: refill
description: Rotate Mystery Box image models from available providers Use when this capability is needed.
metadata:
  author: api-haus
---

Refresh the Mystery Box image family by selecting new models from all available image providers.

## Usage

```
/refill
```

## Process

### 1. Gather Available Models

#### 1a. Fetch KIE.ai Catalog

Fetch the KIE.ai model catalog:
```
WebFetch https://docs.kie.ai/llms.txt
```

Extract all **image-to-image / edit** model IDs. Text-to-image-only models are not eligible for Mystery Box (which requires editing support).

#### 1b. Read Already-Implemented Models

Read the config files for all integrated image providers to see what's already implemented and tested:

- `src/providers/kie-ai/models.ts` — `KIE_AI_IMAGE_MODELS` array: KIE model definitions with `kieModelId`, capabilities, costs
- `src/providers/runware/image-models.ts` — `RUNWARE_IMAGE_MODELS` array: Runware model definitions with `runwareModel` AIR identifiers, capabilities, costs
- `src/providers/image-models.ts` — `IMAGE_MODELS` array: all user-facing model entries including the 4 Mystery Box entries (`family: "mystery"`)

Currently integrated image providers:
- **KIE.ai** (`src/providers/kie-ai/`) — primary source, many models via API. Mystery Box models use KIE provider IDs (prefix `kie/`).
- **Runware** (`src/providers/runware/`) — 12 image models including Flux 2, Wan2.6, Riverflow, Z-Image, Kandinsky, P-Image, Bria FIBO, and **NanoBanana Pro (Google Imagen 3)** via `google:4@2`. Models with `supportsEditing: true` can do i2i via public URL as `seedImage`. Provider IDs use prefix `runware/`.
- **VertexAI** (`src/providers/vertex-ai/`) — Gemini image generation (used for NanoBanana fallback). Not eligible for Mystery Box.

#### 1c. Note Google Models on Runware

Runware hosts Google models that can participate in latency-based fallback. Currently implemented:
- `runware/nano-banana-pro` — Google Imagen 3 (`google:4@2`), $0.138/image, t2i only (no seedImage support)

Check the [Runware models page](https://runware.ai/models) for any new Google models that could be added.

### 2. Understand Current State

Note the current Mystery Box assignment:
- **fast** tier — cheapest model
- **good** tier
- **good+** tier
- **best** tier — most expensive model

Also note which models are already exposed to users through **other families** (NanoBanana, Flux). These should be deprioritized for Mystery Box — the whole point is to give users access to models they can't already pick directly.

### 3. Cross-Reference & Prioritize

Compare the fetched KIE catalog + existing Runware models against what's already configured:
- Which models already have entries in `KIE_AI_IMAGE_MODELS` or `RUNWARE_IMAGE_MODELS`?
- Which are new from the KIE catalog and would need new entries?
- What are the current Mystery Box selections?

**Prioritization rules** (most important first):
1. **Prefer models NOT in regular selection** — models already available as NanoBanana or Flux should be deprioritized. Mystery Box is how users discover new/different models.
2. **Must support image-to-image editing** (`supportsEditing: true`). Skip text-to-image-only models.
3. **Prefer already-implemented models** — models that already exist in `KIE_AI_IMAGE_MODELS` or `RUNWARE_IMAGE_MODELS` with `supportsEditing: true` are proven to work. New unimplemented models need API doc lookup and smoke testing.
4. **Prefer diverse architectures** — mix different model families (Seedream, GPT Image, Wan2.6, Bria, etc.) rather than multiple variants of the same model.
5. **Price tier alignment** — map cheapest eligible models to fast, most expensive to best.

**Eligible Runware models for Mystery Box** (i2i-capable):
- `runware/wan2-6-image` — Wan2.6 ($0.03), seedImage via URL, no strength
- `runware/bria-fibo-edit` — Bria FIBO ($0.04), edit-only (requires image input), uses `inputs.image`
- `runware/flux-2-flex` — Flux 2 Flex ($0.06), seedImage via URL, no strength
- `runware/flux-2-pro` — Flux 2 Pro ($0.06), seedImage via URL, no strength
- `runware/flux-2-max` — Flux 2 Max ($0.07), seedImage via URL, no strength

Note: Runware Flux models are the same architecture as KIE Flux models but via a different API. Using Runware Flux in Mystery Box still overlaps with the user-facing Flux family.

### 4. Look Up API Docs for New KIE Models

For any **new** candidate model from the KIE catalog that doesn't already have an entry in `KIE_AI_IMAGE_MODELS`, fetch its API documentation to determine the correct field names:

```
WebFetch https://docs.kie.ai/market/<vendor>/<model-slug>.md
```

Where the path mirrors the KIE model ID hierarchy (e.g., `seedream/4.5-edit.md` for `seedream/4.5-edit`, `qwen/image-edit.md` for `qwen/image-edit`). Fetch the full URL list from `https://docs.kie.ai/llms.txt` to find exact paths.

Check specifically for:
- **Image input field name**: The API field for image URLs (e.g., `image_input`, `image_urls`, `input_urls`, `image_url`). This maps to `imageFieldName` in config.
- **Single vs array**: Whether the field expects a single URL string or an array. This maps to `imageFieldSingle` in config.
- **Required extra fields**: Any fields beyond prompt/image that are required (e.g., `quality: "basic"`). This maps to `extraInput` in config.
- **Supported aspect ratios and resolutions**: What values the API accepts.

Skip this step for models that already have entries in `KIE_AI_IMAGE_MODELS` — their field names are already known and tested.

### 5. Present Curated Presets

Use `AskUserQuestion` to present **2-4 curated presets**, each being a complete set of 4 models for the Mystery Box tiers.

Design presets considering:
- **Novelty**: prefer models users can't already access through NanoBanana or Flux families
- **Reliability**: prefer already-implemented and smoke-tested models over brand new ones
- **Price tier alignment**: map cheapest models to fast, most expensive to best
- **Model diversity**: mix different providers/architectures when possible
- **Quality progression**: ensure a clear quality/cost step-up from fast to best
- Include the **current selection** as one option (labeled "Current") for comparison

Format each preset clearly showing:
```
Preset Name:
  fast     -> model-name ($X.XX) [kie/runware] [new/existing] [also in: NanoBanana]
  good     -> model-name ($X.XX) [kie/runware] [new/existing]
  good+    -> model-name ($X.XX) [kie/runware] [new/existing]
  best     -> model-name ($X.XX) [kie/runware] [new/existing]
```

Flag any model that overlaps with regular families so the user can make an informed choice. Also indicate which provider (KIE vs Runware) each model comes from.

Use your knowledge of the models (architecture quality, generation speed, typical output quality) to create sensible groupings. If pricing info isn't in the catalog, use `fallbackCredits` from existing config or reasonable estimates based on similar models.

### 6. Apply Selected Preset

After the user picks a preset, update the config files:

#### 6a. For KIE.ai models — Update `src/providers/kie-ai/models.ts`

For any KIE model in the chosen preset that doesn't already have an entry in `KIE_AI_IMAGE_MODELS`, add a new entry. Use the API docs fetched in step 4 to set correct field names:

```ts
{
  id: "kie/<short-name>",
  kieModelId: "<kie-api-model-id>",
  name: "<Display Name>",
  maxResolution: <number>,
  supportsEditing: true,
  supportsResolution: <boolean>,
  fallbackCredits: <number>,
  supportedAspectRatios: [...],
  supportsNativeAutoAspect: <boolean>,
  imageFieldName: "<field-name>",       // from API docs, omit if default "image_input"
  imageFieldSingle: <boolean>,          // omit if false (array is default)
  extraInput: { ... },                  // omit if no extra fields required
},
```

For new models where API docs are unclear, use conservative defaults:
- `maxResolution: 1024`
- `supportsEditing: true` (required for Mystery Box)
- `supportsResolution: false`
- `supportsNativeAutoAspect: false`
- `supportedAspectRatios: ["1:1", "16:9", "9:16", "3:4", "4:3"]` (safe default)
- `imageFieldName: "image_urls"` (most common for edit models)

#### 6b. For Runware models — No model config changes needed

Runware models are already defined in `src/providers/runware/image-models.ts`. No changes needed there — just reference their existing `id` in the Mystery Box entries.

#### 6c. Update `src/providers/image-models.ts`

Update the 4 Mystery Box entries (the ones with `family: "mystery"`) to point to the new models. For each entry update:
- `id` — must match a provider model `id` (e.g., `"kie/seedream-4.5-edit"` or `"runware/wan2-6-image"`)
- `providerId` — same as `id`
- `costUsd` — appropriate cost for the tier
- `supportedResolutions` — from the provider model config (`maxResolution` determines available resolutions)
- `supportedAspectRatios` — from the provider model config
- `supportsNativeAutoAspect` — from the provider model config
- `supportsEditing` — must be `true` for Mystery Box

Keep unchanged: `name`, `family`, `quality`, `supportsChat: false`, `modelConfig` (if any).

### 7. Verify

#### 7a. Type-check

```bash
pnpm exec tsc --noEmit
```

#### 7b. Smoke Test New Models

Run the E2E smoke test filtered to just the new models to verify they actually work with the API:

```bash
SMOKE_MODELS=<comma-separated-model-tokens> pnpm test:e2e:smoke
```

Where `<comma-separated-model-tokens>` are substring matches against model IDs. For example:
```bash
SMOKE_MODELS=seedream-4.5-edit,wan2-6 pnpm test:e2e:smoke
```

The smoke test covers both KIE and Runware providers. Each model should:
- Return a valid image (URL or base64)
- Report `usage.costUsd > 0`
- Complete within 120s

If any model fails, investigate the error and either fix the config (wrong field name, missing extra input) or replace with another candidate.

### 8. Summary

Show the user a summary of what changed, including:
- Models added/removed from provider configs
- Mystery Box tier assignments before → after
- Which provider each model uses (KIE vs Runware)
- Smoke test results (pass/fail, cost, latency per model)

## Important Notes

- Only rotate Mystery Box models (`family: "mystery"`). Never touch NanoBanana or Flux entries.
- The `id` and `providerId` fields in `IMAGE_MODELS` Mystery Box entries must exactly match a model `id` in either `KIE_AI_IMAGE_MODELS` or `RUNWARE_IMAGE_MODELS`.
- Exclude `google/nano-banana` / `nano-banana-pro` from Mystery Box candidates (it's our own model, used in the NanoBanana family).
- **Prefer models NOT already in regular families** (NanoBanana, Flux). Mystery Box should surface different/novel models.
- Only consider models that support image-to-image editing (`supportsEditing: true`). Skip text-to-image-only models when building presets.
- Mystery Box models should have `supportsChat: false`.
- **Always check API docs** for new KIE models before adding them. Each KIE.ai model can have different field names for image input — getting this wrong causes "This field is required" errors in production.
- **Always smoke test** new models before deploying. Use `SMOKE_MODELS=<tokens> pnpm test:e2e:smoke` to verify just the new models.
- Runware models use public URLs for seedImage (via Telegram file proxy). KIE models use their own image URL proxy. Both paths are tested in the smoke suite.

## Known Broken KIE Models (do NOT use)

These KIE market models failed smoke testing (2025-02) and should be skipped:

- **`qwen/image-to-image`** — "broken data stream when reading image file". Image field: `image_url` (single). The model can't read images uploaded via KIE's file upload proxy.
- **`qwen/image-edit`** — Same "broken data stream" error. Image field: `image_url` (single). Same upload proxy incompatibility.
- **`grok-imagine/image-to-image`** — "no image URL found in response". Image field: `image_urls` (array). Task completes but returns no output URL.
- **`ideogram/character-edit`** — Requires `mask_url` + `reference_image_urls` (too complex for Mystery Box).
- **`ideogram/character-remix`** — Requires `reference_image_urls` (too complex for Mystery Box).
- **`4o-image`** — Uses dedicated `/4o-image-api/` endpoint with different field structure (`filesUrl`), not compatible with market `/createTask` API.
- **`flux-kontext`** — Uses dedicated `/flux-kontext-api/` endpoint with `inputImage` field, not compatible with market `/createTask` API.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/api-haus) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
