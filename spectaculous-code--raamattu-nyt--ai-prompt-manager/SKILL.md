---
name: ai-prompt-manager
description: > Use when this capability is needed.
metadata:
  author: spectaculous-code
---

# AI Prompt Manager

**Context first:** Read `Docs/context/ai-system.md` for the system overview.

## Architecture

```
Edge Function (e.g. ai-develop, brainstorm-idea)
  → POST /functions/v1/ai-orchestrator
    → ai-quota.ts    enforceQuotaOrThrow(feature, tokens, userId)
    → ai-config.ts   getFeatureConfig(feature, env) → vendor, model, params
    → ai-prompt.ts   getPrompt(feature, variables, env) → rendered prompts
    → ai-providers.ts callProvider(vendor, model, prompts, params)
    → ai-config.ts   getPricing() + calculateCost()
    → log to ai_usage_logs
  ← { success, content, usage, vendor, model, latencyMs, costUsd }
```

## Orchestrator Request Format

Every edge function calls the orchestrator like this:

```typescript
const res = await fetch(`${supabaseUrl}/functions/v1/ai-orchestrator`, {
  method: "POST",
  headers: { "Content-Type": "application/json", Authorization: authHeader },
  body: JSON.stringify({
    feature: "feature_key",           // Required: from ai_features table
    variables: { key: "value" },      // Template {{key}} substitution
    parseJson: true,                  // Extract & repair JSON from response
    env: "prod",                      // "prod" | "dev" (default: "prod")
    fallbackSystemPrompt: "...",      // Used if DB prompt not found
    fallbackUserPrompt: "...",        // Used if DB prompt not found
  }),
});
```

## Registering a New AI Feature

When an edge function gets `AI_FEATURE_LOCKED / operation_not_found`, the feature isn't registered.

**Required steps** (all 5 in `bible_schema` — missing any one causes runtime errors):

```sql
-- 1. Register feature
INSERT INTO bible_schema.ai_features (key, description, system_id)
VALUES ('my_feature', 'Description', 'raamattu-nyt');

-- 2. Add quota for each plan tier
INSERT INTO bible_schema.ai_plan_quotas (plan_key, feature_key, tokens_per_window, enabled)
VALUES
  ('unauth',  'my_feature', 0,      false),
  ('basic',   'my_feature', 5000,   true),
  ('pro',     'my_feature', 25000,  true),
  ('premium', 'my_feature', 100000, true),
  ('admin',   'my_feature', 999999, true)
ON CONFLICT (plan_key, feature_key) DO NOTHING;

-- 3. Register operation for token pool (can_use_operation checks this)
INSERT INTO bible_schema.ai_operations (operation_key, display_name, description, token_cost, is_active)
VALUES ('my_feature', 'Display Name', 'Description', 10, true)
ON CONFLICT (operation_key) DO NOTHING;

-- 4. Enable operation per plan (can_use_operation checks this for feature_locked)
INSERT INTO bible_schema.plan_feature_access (plan_key, operation_key, is_enabled)
VALUES
  ('guest',   'my_feature', false),
  ('basic',   'my_feature', true),
  ('pro',     'my_feature', true),
  ('premium', 'my_feature', true),
  ('admin',   'my_feature', true)
ON CONFLICT (plan_key, operation_key) DO NOTHING;

-- 5. Configure vendor/model binding (getFeatureConfig checks this — required even for fallback-only!)
INSERT INTO bible_schema.ai_feature_bindings (feature_key, env, ai_vendor, ai_model, param_overrides, is_active)
VALUES ('my_feature', 'prod', 'openrouter', 'deepseek/deepseek-chat-v3-0324',
  '{"max_tokens": 2000, "temperature": 0.7}', true);

-- (Optional) Add to FEATURE_TO_OPERATION_MAP in ai-quota.ts if feature key differs from operation key
```

> **Error mapping:**
> - Missing `ai_operations` → `operation_not_found` (403)
> - Missing `plan_feature_access` → `feature_locked` (403)
> - Missing `ai_feature_bindings` → `Feature not configured or inactive` (500)

**Optional** — if the feature uses DB-managed prompts (not just fallback):

```sql
-- 6. Create prompt template
INSERT INTO bible_schema.ai_prompt_templates (task, name, description)
VALUES ('my_feature', 'Display Name', 'What it does');

-- 7. Create prompt version with {{variables}}
INSERT INTO bible_schema.ai_prompt_versions (
  template_id, version, system_prompt, user_prompt_template, output_schema, status
) VALUES (
  (SELECT id FROM bible_schema.ai_prompt_templates WHERE task = 'my_feature'),
  1, 'System prompt...', 'User prompt with {{var}}...', '{}', 'published'
);

-- 8. Bind to environment
INSERT INTO bible_schema.ai_prompt_bindings (task, env, prompt_version_id, enabled)
VALUES ('my_feature', 'prod',
  (SELECT id FROM bible_schema.ai_prompt_versions
   WHERE template_id = (SELECT id FROM bible_schema.ai_prompt_templates WHERE task = 'my_feature')
   AND version = 1), true);

```

## Prompt Template System

**Variable syntax:** `{{variableName}}` — replaced at render time.

**Resolution order:**
1. `ai_prompt_bindings` for task + env + enabled → specific version
2. Fallback: latest published `ai_prompt_versions` for the task
3. Fallback: `fallbackSystemPrompt` + `fallbackUserPrompt` from request

Most IdeaMachina features use **fallback prompts only** (no DB prompt). Raamattu Nyt features typically use DB-managed prompts. Exception: `summarize_reference_urls` is an IdeaMachina feature that uses **DB-managed prompts** (task-based prompt with `{{variables}}`).

## Provider Configuration

| Vendor | Endpoint | Notes |
|--------|----------|-------|
| `openai` | api.openai.com | GPT-4o, GPT-4o-mini, O3, O4 |
| `anthropic` | api.anthropic.com | Claude models |
| `openrouter` | openrouter.ai | Model aggregator with fallbacks |
| `lovable` | ai.gateway.lovable.dev | Internal gateway |

**Newer models** (gpt-5, o3, o4, gpt-4.1+): Use `max_completion_tokens` instead of `max_tokens`, no `temperature`.

See [providers.md](references/providers.md) for selection strategy.

## Quota System

Token pool per user per rolling window (default 360 min).

**RPC:** `can_use_operation(p_operation_key)` → `{ allowed, reason, cost, balance_after }`

**HTTP error codes:**
- 401 = auth required
- 403 = feature locked for plan (or `operation_not_found`)
- 429 = quota exceeded

**`FEATURE_TO_OPERATION_MAP`** in `ai-quota.ts` maps feature keys to operation keys. If your feature key matches the operation key, no mapping needed.

## Database Tables

| Table | Purpose |
|-------|---------|
| `ai_features` | Feature registry (key, description, system_id) |
| `ai_plan_quotas` | Quota per plan per feature (tokens_per_window, enabled) |
| `ai_prompt_templates` | Prompt structure (task, name) |
| `ai_prompt_versions` | Prompt content with `{{variables}}`, status: draft/published |
| `ai_prompt_bindings` | Link prompt version → env (prod/dev) |
| `ai_feature_bindings` | Link feature → vendor/model/params per env |
| `ai_pricing_latest` | Cost per vendor/model (view) |
| `ai_usage_logs` | Usage tracking with hash dedup |
| `plan_feature_access` | Per-plan operation enable/disable (checked by `can_use_operation`) |

## Common Operations

See [sql-patterns.md](references/sql-patterns.md) for full SQL workflows:
- Create new feature + prompt + bindings
- Version a prompt (draft → published)
- Switch vendor/model
- Query current config
- Monitor usage and costs

## Frontend AI UI Components

Use `@repo/ui` components for all AI operation UIs. See [ui-components.md](references/ui-components.md) for full API.

| Component | When to use |
|-----------|-------------|
| `AIProgressBar` | Simple progress with message + timer, or multi-step progress |
| `AIContextPreview` | Show what context is sent to AI (collapsible, color-coded) |
| `AIProcessingPanel` | **Preferred**: combines AIContextPreview + AIProgressBar in one panel |
| `AIErrorBanner` | AI error display with optional dismiss + timeout hint |
| `useElapsedTimer` | Hook for mutation duration tracking (`start` in onMutate, `stop` in onSettled) |
| `isTimeoutError` | Detect timeout patterns in error messages |

**Standard pattern:** `AIProcessingPanel` during `mutation.isPending` + `AIErrorBanner` on error + `useElapsedTimer` for duration.

## Key Files

| File | Purpose |
|------|---------|
| `supabase/functions/ai-orchestrator/index.ts` | Central gateway |
| `supabase/functions/_shared/ai-prompt.ts` | getPrompt, renderTemplate |
| `supabase/functions/_shared/ai-providers.ts` | callProvider, parseJsonResponse |
| `supabase/functions/_shared/ai-config.ts` | getFeatureConfig, getPricing |
| `supabase/functions/_shared/ai-quota.ts` | enforceQuotaOrThrow |
| `hooks/useAIQuota.ts` | Frontend token balance |
| `pages/AdminAIPage.tsx` | Admin hub (/ohjaamo/ai) |
| `packages/ui/src/ai-*.tsx` | AI UI components (progress, context, error) |
| `packages/ui/src/use-elapsed-timer.ts` | Elapsed timer hook |

## Admin Panel

`/ohjaamo/ai` — 6 tabs: Ominaisuudet, Promptit, Hinnoittelu, Käyttö, Palautteet, Testaus

## Skills Handoff

- **Quota/plan limits** → `subscription-system` skill
- **Edge Function creation** → `edge-function-generator` skill
- **Cost optimization** → `performance-auditor` skill

## References

- [sql-patterns.md](references/sql-patterns.md) — Common SQL workflows
- [providers.md](references/providers.md) — Vendor/model selection
- [ui-components.md](references/ui-components.md) — AI UI components (AIProgressBar, AIProcessingPanel, AIContextPreview, AIErrorBanner)
- `Docs/context/ai-system.md` — System overview
- `Docs/06-AI-ARCHITECTURE.md` — Full architecture doc

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spectaculous-code) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
