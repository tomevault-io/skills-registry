---
name: idea-pricing-discovery
description: Idea Machina: Adaptive Pricing Discovery - a resumable AI-powered pipeline that discovers optimal pricing strategies for product ideas. 12-step state machine with two routes (strong/weak context), user approval gates, and tiered pricing output. Use when (1) developing or debugging the pricing discovery pipeline, (2) modifying the state machine or step logic, (3) working on PricingDiscoveryDialog or PricingResultView components, (4) editing pricing prompt templates in ai_prompt schema, (5) modifying the pricing-discovery edge function, (6) working with PricingInputSchemaV1 or PricingResult types, (7) adding new pricing steps or approval gates, (8) debugging run status/resumption issues, (9) changing AI model bindings for pricing features. Use when this capability is needed.
metadata:
  author: spectaculous-code
---

# Adaptive Pricing Discovery

Resumable AI pipeline: idea context → analyze strength → generate/approve hypotheses → compile schema → produce tiered pricing.

## State Machine

```
Strong Route (context score = strong):
S0_NORMALIZE → S1_ANALYZE → S2A_SYNTHESIZE → S3_COMPILE → S4_IDEATION → COMPLETED

Weak/Medium Route (hypothesis-driven with user feedback):
S0 → S1 → S2B1_VALUE → U1 [user picks] → S2B2_BUYER → U2 [user picks] → S2B3_MARKET → U3 [user picks] → S3 → S4 → COMPLETED

Rejection fallback (at U1/U2):
→ ASK_MINIMUM [user provides text] → S3 → S4 → COMPLETED
```

**Statuses:** `running` | `awaiting_user` | `completed` | `failed`

**Pause points:** U1 (value hypotheses), U2 (buyer hypotheses), U3 (market anchors), ASK_MINIMUM

## Key Files

### Edge Function
| File | Purpose |
|------|---------|
| `supabase/functions/pricing-discovery/index.ts` | State machine, orchestrator calls, DB state, resumption |

### Types
| File | Purpose |
|------|---------|
| `apps/idea-machina/src/types/pricing.ts` | PricingInputSchemaV1, PricingResult, PricingRun, step types |

### Components
| Component | File | Purpose |
|-----------|------|---------|
| `PricingDiscoveryDialog` | `components/PricingDiscoveryDialog.tsx` | Main dialog: states (idle/loading/u_step/ask_minimum/completed/failed) |
| `PricingOptionCard` | `components/pricing/PricingOptionCard.tsx` | Choice card with checkbox/radio + confidence badge |
| `PricingResultView` | `components/pricing/PricingResultView.tsx` | Final result: tiers, sanity, stress test, warnings |

### Service Layer
| File | Functions |
|------|-----------|
| `lib/ideas.ts` | `startPricingDiscovery(ideaId)`, `resumePricingDiscovery(runId, action, selections)` |
| `lib/aiContext.ts` | `pricing_discovery` as AiPhaseType |
| `lib/pipelineStatus.ts` | "pricing" chip in status bar |

### Database
| Migration | Purpose |
|-----------|---------|
| `20260211000000_pricing_discovery_run_table.sql` | `ai_prompt.pm_pricing_discovery_runs` table + RLS |
| `20260211000100_pricing_discovery_features.sql` | 8 AI features + quotas + operations |
| `20260211000200_pricing_discovery_prompts.sql` | All prompt templates (S0-S4, U1-U3) |

## DB Table: `ai_prompt.pm_pricing_discovery_runs`

| Column | Type | Notes |
|--------|------|-------|
| id | uuid | PK |
| idea_id | uuid | FK pm_ideas |
| current_step | text | S0→S4, U1-U3, ASK_MINIMUM, COMPLETED |
| status | text | running, awaiting_user, completed, failed |
| step_results | jsonb | Intermediate outputs per step |
| approvals | jsonb | User decisions at approval points |
| pricing_schema | jsonb | PricingInputSchemaV1 (from S3) |
| pricing_result | jsonb | Final output (from S4) |
| route | text | strong or medium_weak |
| total_tokens_used | int | |
| total_cost_usd | numeric | |
| error_message | text | If failed |
| created_by | uuid | User |

## AI Features (8 steps)

| Feature Key | Step | Model |
|-------------|------|-------|
| `pricing_s0_normalize` | Ingest & normalize | Sonnet 4.5 |
| `pricing_s1_analyze` | Context strength | Sonnet 4.5 |
| `pricing_s2a_synthesize` | Full-context (strong) | Sonnet 4.5 |
| `pricing_s2b1_value` | Value hypotheses | Sonnet 4.5 |
| `pricing_s2b2_buyer` | Buyer hypotheses | Sonnet 4.5 |
| `pricing_s2b3_market` | Market anchors | Sonnet 4.5 |
| `pricing_s3_compile` | Schema compilation | Sonnet 4.5 |
| `pricing_s4_ideation` | Final pricing | Sonnet 4.5 |

## Edge Function API

**Start:** `{ mode: "start", idea_id }` → runs S0→S1→(route)→pause or complete

**Resume:** `{ mode: "resume", run_id, action, selections? }` where action = approve|reject|skip|minimum_answers

**Response:** `{ success, run_id, status, current_step, options?, result?, pricing_schema?, usage }`

## PricingInputSchemaV1 (key fields)

```
value_definition: { core_value_proposition, value_type, value_levers, perceived_uniqueness }
tier_intents: [{ tier_name, target_segment, positioning, key_features }]
buyer_context: { buyer_type, payer_types, budget_sensitivity, purchase_type }
market_anchors: { reference_products[], psychological_alternatives[] }
stress_test_inputs: { churn_scenario, price_elasticity_guess, volume_assumption }
```

## Common Patterns

### Add new step to pipeline
1. Add step constant to edge function step enum
2. Add prompt template migration with `ai_prompt.prompts` + `ai_prompt.prompt_versions`
3. Register AI feature + quota in `bible_schema.ai_features` + `ai_plan_quotas`
4. Add step logic in edge function state machine (after which step, pause or auto-continue)
5. Update progress percentage mapping in `PricingDiscoveryDialog`
6. Add types for step output in `types/pricing.ts`

### Modify prompt template
1. Find prompt by `feature_key` in `ai_prompt.prompts`
2. Create new `prompt_versions` row (keep old version for rollback)
3. Update `ai_prompt.ai_feature_bindings` if changing model/vendor
4. Test via edge function (start a new pricing run)

### Modify dialog UI at a U-step
1. `PricingDiscoveryDialog.tsx` — handles step rendering logic
2. `PricingOptionCard.tsx` — individual choice card
3. Step data comes from edge function `options` field in response
4. User selection sent back via `resumePricingDiscovery(runId, action, selections)`

### Debug a stuck/failed run
1. Query: `SELECT * FROM ai_prompt.pm_pricing_discovery_runs WHERE id = '<run_id>'`
2. Check `status`, `current_step`, `error_message`
3. Check `step_results` jsonb for partial outputs
4. Edge function logs: check `pricing-discovery` in Supabase logs

## References

- **Full schema details**: See [references/schema.md](references/schema.md) (pricing runs table, AI features, prompts)
- **Parent skill**: `idea-machina` for general Idea Machina development

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spectaculous-code) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
