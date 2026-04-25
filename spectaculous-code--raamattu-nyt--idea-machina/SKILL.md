---
name: idea-machina
description: | Use when this capability is needed.
metadata:
  author: spectaculous-code
---

# IdeaMachina Development Guide

## App Location

```
apps/idea-machina/src/
├── ai/
│   ├── prompts/          # AI prompt templates (*.md)
│   └── runner/           # AI module executor (runModule, buildModuleInput, types)
├── components/
│   ├── evolution/        # Evolution system UI
│   │   ├── stages/       # CoreStage, DirectionStage, ForceStage, SparkStage, SparkCard
│   │   └── sidebar/      # AIContextPanel, ContextPanel, StageChecklist, TimelinePanel
│   ├── ideas/            # Legacy ideas UI (IMIdeaCard, IdeasStatsBar, etc.)
│   ├── pricing/          # Pricing discovery UI
│   └── checklist/        # Project setup checklist
├── hooks/
│   └── evolution/        # Evolution hooks (Provider, useEvolution, mutations)
├── lib/                  # Business logic, routing, health checks
├── pages/                # All pages
├── stores/__tests__/     # Evolution store tests
├── test-utils/           # Test factories & adapters
├── types/                # TypeScript types
└── App.tsx               # Routes + providers
```

## Architecture: Two Systems

### 1. Evolution System (Primary, Active)

4-stage progressive pipeline: **KIPINÄ → YDIN → SOIHTU → VALO**

| Stage | Finnish | Meaning | Icon | Color | DB Table | Key Type |
|-------|---------|---------|------|-------|----------|----------|
| KIPINÄ (Spark) | ideat | Raw ideas, AI-developed proposals | Flame | orange | `pm_sparks` | `Spark` |
| YDIN (Core) | strategia | Distilled problem/target/outcome | CircleDot | blue | `pm_cores` | `Core` |
| SOIHTU (Torch) | toteutus | Execution modules (pricing, validation, GTM, roadmap, CTA slogan, SOME marketing) | Zap | violet | `pm_force_modules` | `ForceModule` |
| VALO (Light) | vaikutus | Measured impact, proven value | Sun | yellow | — | — |

> **Note:** The SUUNTA (Direction) stage has been removed from the UI. `pm_directions` table and hooks still exist at DB level but there is no `DirectionStage` component. Direction data (segments, customer_personas) persists but is accessed differently. The `outcome` field now lives directly on `Core` (replacing the old `value_shift` field).

**Stage Status**: `empty | locked | in-progress | done | skipped`
Stages unlock sequentially. Activation modes can skip stages.

**Landing page model section** (`IdeaMachinaLandingPage.tsx`): Shows all 4 stages in a grid with Finnish names + subtitle (e.g., "KIPINÄ" / "ideat"). i18n keys: `landing.stages.*Fi`, `landing.model.*Subtitle`, `landing.model.*`.

#### Spark Zones (AHJO / HAUTOMO / SOIHTU)

Sparks are split into three zones controlled by `pm_sparks.shelved` and `pm_sparks.usage_state`:

| Zone | Finnish | Filter | Default State | Icon | Color |
|------|---------|--------|---------------|------|-------|
| **AHJO** (Forge) | Aktiivisesti kehitettävät | `!shelved && usage_state != 'implemented'` | Expanded, open | Flame | Orange |
| **HAUTOMO** (Incubator) | Hautumassa olevat | `shelved && usage_state != 'implemented'` | Collapsed | Egg | Amber |
| **SOIHTU** (Torch) | Toteutetut kipinät | `usage_state == 'implemented'` | Collapsed | CircleCheckBig | Green |

- `SparksSection` accepts `zone?: "ahjo" | "hautomo" | "soihtu"` prop to filter sparks
- `useSparkMutations.setShelved(id, shelved)` toggles AHJO ↔ HAUTOMO
- `useSparkMutations.setImplemented(id, implemented)` toggles SOIHTU ↔ AHJO
- `LandingSparkCard` shows shelve/unshelve + implemented toggle buttons
- SOIHTU sparks show green "Toteutettu" badge in collapsed header
- `EvolutionPage` renders three `CollapsibleSection` wrappers: AHJO (orange) + HAUTOMO (amber) + SOIHTU (green)
- HAUTOMO + SOIHTU sparks auto-collapse on load

**AI Context Priority:** SOIHTU → AHJO → HAUTOMO (implemented sparks are confirmed core features)

#### Source Spark / LÄHDEKIPINÄ

Sparks track their origin via `source_spark_id`. When a core is created from a spark, the core stores `source_spark_id` → that spark is the "YDINKIPINÄ" (core spark).

- `LandingSparkCard` accepts `isSourceSpark?: boolean` prop
- When `isSourceSpark`, all action buttons are hidden (read-only display with rating)
- `CoreSummaryCard` shows YDINKIPINÄ section: renders `LandingSparkCard` with `isSourceSpark` for the core's source spark
- Lookup: `sparks.find(s => s.id === core.source_spark_id)`

#### Kipinäsuihku (Bulk Spark Import)

`BulkSparkImport` component: paste text → AI parses into explicit + implicit sparks.

- 2-pass client-side flow: `normalize_bulk_text` → `parse_bulk_sparks` (split to avoid edge function timeout)
- Typed outputs: `NormalizeBulkTextOutput`, `ParseBulkSparksOutput`, `GenerateImplicitSparksOutput`
- Distinguishes `explicit_sparks` (directly mentioned) and `implicit_sparks` (inferred)
- Hierarchical parent-child relationships with per-spark accept/reject
- Shows source excerpts, reasoning, and 2-line pass status bar
- Persistent error banner on timeout/error

### 2. Legacy Ideas System

Classic CRUD with status flow: `new → nurturing → ready → converted | archived`
- Tables: `pm_ideas`, `pm_idea_tags`, `pm_idea_ratings`, `pm_projects`
- "Continue to..." actions convert ideas to projects/goals/workflows/prompts

## Routes

| URL | Page | Notes |
|-----|------|-------|
| `/idea-machina` | LandingPage | Hero spark input |
| `/idea-machina/evolve` | EvolutionPage | Main workflow |
| `/idea-machina/evolve?stage=spark` | SparkStage | Loose sparks |
| `/idea-machina/evolve?coreId=X` | Core view | Specific core |
| `/idea-machina/core` | ProjectsPage | Core management |
| `/idea-machina/plans` | PlansPage | Subscription plans |
| `/ideas` | IdeasPage | Legacy ideas list |
| `/ideas/:id` | IdeaDetailPage | Legacy idea detail |

## Evolution Data Flow

```
EvolutionProvider (auto-creates evolution per user)
  → useEvolutionData (React Query ↔ Supabase ai_prompt schema)
    → useEvolution (facade hook)
      → useSparkMutations, useCoreMutations, useDirectionMutations, useForceMutations
        → Optimistic updates + toast feedback
```

**Key Tables**: `pm_sparks`, `pm_cores`, `pm_force_modules`, `pm_evolutions` (also `pm_directions` at DB level, but UI removed)

**Provider Pattern**:
- `useEvolutionId()` — throws if no evolution (authenticated only)
- `useEvolutionIdOptional()` — returns null for guests

## AI Module System

11 runnable modules via `ai/runner/runModule.ts` → Edge Function `ai-run-module`:

| Module | Purpose |
|--------|---------|
| `ai_develop` | Analyze + generate proposals (refine/split/derive/reframe/upgrade) |
| `generate_new_ideas` | Create 5+ sparks from context |
| `brainstorm_idea` | Brainstorm angles, next steps, wild cards |
| `clarification_mode` | Ask follow-up questions |
| `instant_activation` | Direct 1-phase execution plan |
| `structured_activation` | Multi-phase execution plan |
| `attach_to_core` | Recommend core attachment |
| `upgrade_entity_context` | Suggest context improvements |
| `parse_bulk_sparks` | Kipinäsuihku: parse text into explicit + implicit sparks |
| `normalize_bulk_text` | Kipinäsuihku pass 1: normalize raw text |
| `generate_implicit_sparks` | Kipinäsuihku: infer implicit sparks from text |

**Persona Generation** (not a prompt module — client-side AI function):
- `generateCustomerPersonaCandidates()` in `lib/evolution-ai.ts`
- `GeneratePersonasDialog` component for accept/reject flow
- Personas stored in `pm_customer_personas` table (linked to direction)

Concurrency guard in `runModule`: `inFlight` Set prevents duplicate calls for the same module.

## AI Context Modes (LEAN / FULL)

Evolution AI uses `buildProjectContext(data, mode)` to control context sent to AI:

| Mode | Sparks | Core | Force | reference_urls |
|------|--------|------|-------|----------------|
| **LEAN** | Titles only | name + one_liner | null | stripped |
| **FULL** | Full content | All fields | Full | stripped → url_summaries |

**Key rules:**
- Raw `reference_urls` are **never** sent to AI — stripped in `buildProjectContext` (full mode) and `developCore`
- Premium users get `url_summaries`: AI-summarized URL content (max ~600 chars/URL) via `summarize-urls` edge function
- `UrlSummary { url, summary, chars, fetched_at }` — stored in `pm_cores.url_summaries` jsonb
- `IMContextPreview` (wrapper around shared `AIContextPreview`) shows FULL/LEAN badge + "URL-lähteet" section with domain tags
- Core section in context bar shows only `core.name` (no one_liner)
- All AI dialogs (GenerateSparks, ClarifyCore) use `mode="full"`
- `LandingSparkCard` shows `IMContextPreview` during AI processing

**Files:** `ai/runner/buildProjectContext.ts` (mode logic), `lib/evolution-ai.ts` (summarizeReferenceUrl), `types/project-evolution.ts` (UrlSummary), `components/evolution/AIContextPreview.tsx` (IMContextPreview)

## Key Business Logic

| File | Purpose |
|------|---------|
| `lib/activation-router.ts` | Route UI actions → AI modules (safety gates, confidence thresholds) |
| `lib/intent-router.ts` | Pure routing from classified intent |
| `lib/activation-mode.ts` | Recommend activation mode (instant/structured/persistent) |
| `lib/coreHealthCheck.ts` | Derive actionable improvements from core state |
| `lib/evolution-ai.ts` | AI develop, classify intent, promote spark, summarize URLs, `classifyAndRun()` (classify→route→run pipeline) |
| `lib/contextTiers.ts` | Context layer matrix for legacy AI features |
| `lib/pipelineStatus.ts` | Pipeline state: idea → goals → roadmap → app |

## Key Components

| Component | Purpose |
|-----------|---------|
| `LandingSparkCard` | Full spark interaction: AI dev, proposals, core picker, shelve toggle |
| `SparksSection` | Filtered spark list with zone support (ahjo/hautomo/soihtu), toolbar, collapse/expand |
| `CoreFormSection` | Core editing: name, one_liner, problem, target, outcome, non_goals, URL digests (outcome replaced old value_shift) |
| `GeneratePersonasDialog` | AI persona generation with accept/reject candidates |
| `CoreSummaryCard` | Core summary with status badge, collapsible chip sections, YDINKIPINÄ source spark |
| `KirkastaPanel` | AI health check actions panel |
| `CreateCoreDialog` | Blank core creation |
| `CoresPageHeader` | Core management page header |
| `GenerateSparksDialog` | Batch spark generation |
| `BulkSparkImport` | Kipinäsuihku: paste text → AI parses into explicit + implicit sparks |
| `CoreActionsPanel` | Contextual action suggestions |
| `SparkActivityLog` | Spark change history |
| `IMContextPreview` | AI context bar showing sparks/core/direction/force with mode badge |
| `ModuleResultCard` | AI module results + ClarificationPanel with free-text option |
| `CollapsibleSection` | Reusable collapsible wrapper with chevron, used for AHJO/HAUTOMO |
| `ExpandableText` | Reusable text truncation with "Lue lisää"/"Näytä vähemmän" toggle |
| `StageNavBar` | 4-stage navigation bar |

## UI Patterns

### Ref-based Focus on Edit Mode Entry (pendingAiPromptFocus)

When a button needs to open edit mode AND focus a specific field:

```tsx
const targetRef = useRef<HTMLTextAreaElement>(null);
const pendingFocus = useRef(false);

// In button onClick:
pendingFocus.current = true;
setEditing(true);

// useEffect to apply focus after render:
useEffect(() => {
  if (editing && pendingFocus.current) {
    pendingFocus.current = false;
    requestAnimationFrame(() => targetRef.current?.focus());
  }
}, [editing]);
```

Used by: AI-ohje button in `LandingSparkCard`.

### ExpandableText Component

Reusable overflow detection + toggle for long text:

```tsx
<ExpandableText text={content} lineClamp="line-clamp-3" />
```

- Auto-detects overflow via `scrollHeight > clientHeight + 1`
- i18n keys: `evolution.spark.showAll`, `evolution.spark.showLess`
- Used in: `CoreSummaryCard` for `core.one_liner`

### CollapsibleChipSection (inline in CoreSummaryCard)

Core chip arrays (problem/target/outcome) rendered as collapsible badge groups with chevron toggle. Defined inline in `CoreSummaryCard` — not a standalone component.

### LandingSparkCard `isSourceSpark` Mode

Read-only spark display with rating. Hides all action buttons. Used when rendering source sparks inside `CoreSummaryCard`.

## References

- [Architecture Details](references/architecture.md) — Component hierarchy, data flow, query keys, URL routing
- [DB Schema](references/db-schema.md) — Table structures, RLS policies

## Common Tasks

### Add New Spark Field

1. Migration: `ALTER TABLE ai_prompt.pm_sparks ADD COLUMN ...`
2. Types: Update `Spark` in `types/project-evolution.ts`
3. Data: Update `useEvolutionData.ts` select query
4. Mutations: Update `useSparkMutations.ts`
5. UI: Update `LandingSparkCard.tsx` or `SparkCard.tsx`

### Add New Core Field

1. Migration: `ALTER TABLE ai_prompt.pm_cores ADD COLUMN ...`
2. Types: Update `Core` in `types/project-evolution.ts`
3. Data: Update `useEvolutionData.ts` select query
4. Mutations: Update `useCoreMutations.ts`
5. UI: Update `CoreFormSection.tsx`
6. i18n: Add key to `evolution.core.*` in both locale files

### Add New AI Module

1. Create prompt: `ai/prompts/module_name.md`
2. Register in `ai/prompts/index.ts`
3. Add output type to `ModuleOutputMap` in `ai/runner/types.ts`
4. Add input builder in `ai/runner/buildModuleInput.ts`
5. Wire into activation router if needed

### Add New Evolution Stage Feature

1. Reference `useEvolutionComputed.ts` for stage unlocking logic
2. Create stage component in `components/evolution/stages/`
3. Add to `StageContent.tsx` switch
4. Update `stageConfig.ts` metadata
5. Add mutation hook in `hooks/evolution/`

### Add New Legacy Idea Field

1. Migration: `ALTER TABLE ai_prompt.pm_ideas ADD COLUMN ...` (+ `_fi`/`_en`)
2. Types: Update `PmIdea` and `IdeaFormData` in `types/ideas.ts`
3. Form: Add `LanguageFieldTabs` in `IdeaForm.tsx`
4. Card: Display in `IMIdeaCard.tsx`
5. Translate: Add to `TranslationResult` in `lib/ideas.ts`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spectaculous-code) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
