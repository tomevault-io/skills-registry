---
name: productpix-navigator
description: Navigate ProductPix codebase with architecture maps and data flow traces. Use when searching src/lib/, src/app/api/, src/components/, or prompt-bench/. Covers create_generation_job RPC, credit system, migrations, EmptyState, Toast components. Use for "where is X defined", "how does Y work", database queries, API routes, or component locations. Triggers on lib/db, lib/ai, lib/r2, route.ts, page.tsx, supabase/migrations, or any exploration of generation/upload/payment flows. (project) Use when this capability is needed.
metadata:
  author: andrewske
---

# ProductPix Navigator

**Stop opening files blindly.** This skill provides comprehensive architecture and navigation context for the ProductPix codebase.

## Quick Architecture Overview

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│   Browser       │────▶│   Next.js 16    │────▶│   Supabase      │
│   (Client)      │     │   App Router    │     │   PostgreSQL    │
└─────────────────┘     └─────────────────┘     └─────────────────┘
         │                      │                        │
         │ presigned            │ rawPrompt +            │ RPC calls
         │ URLs                 │ image-to-image         │ (create_job,
         ▼                      ▼                        │  add_credits)
┌─────────────────┐     ┌─────────────────┐             │
│  Cloudflare R2  │     │   Google        │             │
│  (Images)       │     │   Gemini        │             │
└─────────────────┘     └─────────────────┘             │
                                                         │
                    ┌────────────────────────────────────┘
                    ▼
            ┌─────────────────┐
            │  prompt-bench   │
            │  CLI Testing    │
            └─────────────────┘
```

## System Components

| Layer | Technology | Location | Purpose |
|-------|------------|----------|---------|
| Frontend | Next.js 16 App Router | `src/app/` | UI, routing, server components |
| API | Next.js Route Handlers | `src/app/api/` | Backend logic, validation |
| Database | Supabase (PostgreSQL) | `src/lib/supabase/` | Data persistence, RLS, RPC |
| Storage | Cloudflare R2 | `src/lib/r2/` | Image uploads, presigned URLs |
| AI | Google Gemini | `src/lib/ai/` | Image generation (rawPrompt + i2i) |
| Payments | Stripe | `src/lib/stripe/` | Credit purchases, webhooks |
| Testing | prompt-bench CLI | `prompt-bench/` | Isolated prompt testing framework |
| Pricing | Multi-provider | `src/lib/pricing/` | Cost calculation (Anthropic, OpenAI, Google) |

## Component Boundaries & Responsibilities

### Frontend (src/app/)
- **Owns**: UI rendering, client interactions, routing
- **Talks to**: API routes via fetch/Server Actions
- **Never**: Direct database access, direct R2 access, direct Gemini calls

### API Layer (src/app/api/)
- **Owns**: Business logic, validation, auth checks, RPC orchestration
- **Talks to**: Supabase (via admin client), R2 (presigned URLs), Gemini
- **Never**: UI concerns, client state

### Database (Supabase)
- **Owns**: Data persistence, RLS policies, RPC functions
- **Talks to**: Only API layer (via client/server/admin clients)
- **RPC Functions**: `create_generation_job`, `add_credits`, `consume_credits`, `refund_credits`, `update_prompt_test_status`

### Storage (R2)
- **Owns**: Image file storage
- **Access**: Browser uploads directly via presigned URLs (NOT through server)
- **Confirmation**: Server confirms upload after browser completes

### prompt-bench (CLI)
- **Owns**: Prompt template testing, evaluation, A/B comparison
- **Talks to**: Supabase (templates, tests, evaluations), R2 (images), Gemini
- **Never**: Web app concerns (isolated module with dual tsconfig)

## Entry Points (User Action → File)

| User Action | Entry File | Flow Link |
|-------------|------------|-----------|
| Visit homepage | `src/app/page.tsx` | Landing page |
| Sign up | `src/app/signup/page.tsx` | Auth flow |
| Login | `src/app/login/page.tsx` | Auth flow |
| Generate single image | `src/app/generate/page.tsx` | [Generation flow](#generation-flow) |
| Create batch | `src/app/batch/new/page.tsx` | [Batch flow](#batch-flow) |
| View batch | `src/app/batch/[batchId]/page.tsx` | [Batch flow](#batch-flow) |
| Purchase credits | `src/app/pricing/page.tsx` | [Payment flow](#payment-flow) |
| View dashboard | `src/app/dashboard/page.tsx` | Dashboard |
| Manage settings | `src/app/settings/page.tsx` | Settings |

### API Entry Points

| Endpoint | File | Purpose |
|----------|------|---------|
| `POST /api/generate` | `src/app/api/generate/route.ts` | Single image generation |
| `POST /api/batch` | `src/app/api/batch/route.ts` | Batch creation |
| `GET /api/batch/[id]` | `src/app/api/batch/[id]/route.ts` | Batch status |
| `POST /api/upload` | `src/app/api/upload/route.ts` | Presigned URL generation |
| `POST /api/stripe/checkout` | `src/app/api/stripe/checkout/route.ts` | Create checkout session |
| `POST /api/webhooks/stripe` | `src/app/api/webhooks/stripe/route.ts` | Stripe webhook handler |
| `GET /api/templates` | `src/app/api/templates/route.ts` | List prompt templates |
| `GET /api/images` | `src/app/api/images/route.ts` | List generated images |

## Database Queries (src/lib/db/)

Each file contains domain-specific queries using Supabase client:

| File | Purpose | Key Functions |
|------|---------|---------------|
| `users.ts` | User management | `getUserById`, `getUserByEmail` |
| `credits.ts` | Credit balance | `getUserCredits`, `addCredits` (via RPC) |
| `jobs.ts` | Generation jobs | `createJob` (via RPC), `getJobById`, `updateJobStatus` |
| `generated-images.ts` | Image records | `saveGeneratedImage`, `getImagesByUser` |
| `uploads.ts` | Source images | `createUpload`, `getUploadById` |
| `batch.ts` | Batch operations | `createBatch`, `getBatchById`, `getBatchJobs` |
| `templates.ts` | Prompt templates | `getTemplates`, `getTemplateBySlug`, `saveTemplate` |
| `billing.ts` | Stripe billing | `getCustomerBilling`, `createCheckout` |
| `cost-queries.ts` | Cost tracking | `getGenerationCosts`, `getCostsByModel` |

## Supabase Client Variants (src/lib/supabase/)

**Critical**: Use the right client for the context!

```typescript
// Browser client - RLS enforced, uses cookies
import { createClient } from '@/lib/supabase/client'

// Server client - SSR, RLS enforced, uses headers
import { createClient } from '@/lib/supabase/server'

// Admin client - BYPASSES RLS, for system operations
import { getAdminClient } from '@/lib/supabase/admin'
```

## Generation Flow

```
1. User clicks "Generate" in UI
   └─▶ src/app/generate/page.tsx (form submission)

2. Form submits to API
   └─▶ POST /api/generate (src/app/api/generate/route.ts)

3. API validates & creates job
   └─▶ createJob() RPC call (src/lib/db/jobs.ts)
   └─▶ consume_credits RPC (checks balance, deducts)

4. API calls Gemini
   └─▶ geminiProvider.rawPrompt() (src/lib/ai/providers/gemini.ts)
   └─▶ Google Gemini API

5. Image generated
   └─▶ Base64 response from Gemini

6. Store image in R2
   └─▶ uploadToR2() (src/lib/r2/index.ts)
   └─▶ Cloudflare R2 bucket

7. Save record to database
   └─▶ saveGeneratedImage() (src/lib/db/generated-images.ts)

8. Update job status
   └─▶ updateJobStatus() (src/lib/db/jobs.ts)

9. Return response
   └─▶ { imageUrl, creditsRemaining, jobId }
```

## Upload Flow (Browser → R2)

```
1. User selects file
   └─▶ src/components/forms/* (file input)

2. Request presigned URL
   └─▶ POST /api/upload (src/app/api/upload/route.ts)
   └─▶ getPresignedUrl() (src/lib/r2/index.ts)

3. Browser uploads directly to R2
   └─▶ PUT request to presigned URL
   └─▶ Cloudflare R2 (NOT through server)

4. Confirm upload
   └─▶ createUpload() (src/lib/db/uploads.ts)
   └─▶ Save upload record in Supabase
```

**Key Gotcha**: Browser uploads DIRECTLY to R2 via presigned URL. Server only generates URL and confirms after upload completes.

## Payment Flow

```
1. User clicks "Buy Credits"
   └─▶ src/app/pricing/page.tsx

2. Create Stripe checkout session
   └─▶ POST /api/stripe/checkout (src/app/api/stripe/checkout/route.ts)
   └─▶ stripe.checkout.sessions.create()

3. User completes payment on Stripe
   └─▶ Redirects to Stripe hosted checkout

4. Stripe sends webhook
   └─▶ POST /api/webhooks/stripe (src/app/api/webhooks/stripe/route.ts)
   └─▶ Verify signature, handle event

5. Add credits to user account
   └─▶ add_credits RPC (src/lib/db/credits.ts)
   └─▶ Updates users.credits, creates credit_transaction record

6. Redirect to success page
   └─▶ src/app/success/page.tsx
```

## Batch Flow

```
1. User creates batch
   └─▶ src/app/batch/new/page.tsx (batch form)

2. Submit batch
   └─▶ POST /api/batch (src/app/api/batch/route.ts)
   └─▶ createBatch() (src/lib/db/batch.ts)

3. Create jobs for each item
   └─▶ Loop: createJob() RPC for each batch item

4. Process jobs
   └─▶ Background processing (same as generation flow)

5. View batch status
   └─▶ src/app/batch/[batchId]/page.tsx
   └─▶ GET /api/batch/[id] (src/app/api/batch/[id]/route.ts)
   └─▶ getBatchJobs() (src/lib/db/batch.ts)
```

## prompt-bench Testing Framework

Isolated CLI for prompt template testing (see `prompt-bench.toml` for config).

### Directory Structure
```
prompt-bench/
├── cli.ts                    # Entry point
├── core/                     # Core types, errors, compiler
│   ├── types.ts              # Template, Test, Evaluation types
│   ├── errors.ts             # Result<T> type
│   ├── compiler.ts           # Template variable compilation
│   ├── evaluator.ts          # AI-powered evaluation
│   └── statistics.ts         # Score analysis
├── commands/                 # CLI commands
│   ├── run.ts                # Generate images
│   ├── evaluate.ts           # Score images
│   ├── compare.ts            # A/B testing
│   ├── report.ts             # HTML reports
│   ├── test.ts               # Full pipeline
│   ├── sync.ts               # Sync templates to DB
│   └── improve.ts            # AI-powered iteration
├── templates/                # Template definitions
│   ├── registry.ts           # Template registry
│   └── *.ts                  # Template files
└── db/                       # Database helpers
    └── client.ts             # Supabase admin client
```

### prompt-bench Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `bun bench run` | Generate images | `bun bench run --template ecommerce-base` |
| `bun bench evaluate` | Score images | `bun bench evaluate --label batch-1` |
| `bun bench compare` | A/B testing | `bun bench compare --baseline v1 --candidate v2` |
| `bun bench report` | HTML report | `bun bench report --label batch-1` |
| `bun bench test` | Full pipeline | `bun bench test --template ecommerce-base` |
| `bun bench sync` | Sync templates | `bun bench sync` |
| `bun bench improve` | AI iteration | `bun bench improve --template ecommerce-base` |
| `bun bench open-last-report` | Open last report | `bun bench open-last-report` |

### prompt-bench Key Concepts

- **Templates**: Prompt templates with variables (stored in `prompt-bench/templates/`)
- **Batch files**: JSON test cases with variable values (`fixtures/images-batch.json`)
- **Idempotency**: Tests tracked by hash keys to prevent duplicate evaluations
- **Result type**: Explicit error handling (no exceptions), uses `ok()`/`err()`
- **R2 evaluation**: Evaluator fetches images directly from R2 URLs (not local files)
- **Dual tsconfig**: `tsconfig.json` (CLI + tests) vs `tsconfig.build.json` (Next.js build, excludes prompt-bench/)

## Finding Things (Grep Patterns)

### By Feature
```bash
# Generation-related code
grep -rl "generation\|generate" --include="*.ts" src/app src/lib

# Credit system
grep -rl "credit\|balance" --include="*.ts" src/lib/db src/app/api

# R2 storage
grep -rl "r2\|presigned\|upload" --include="*.ts" src/lib src/app/api

# Gemini AI
grep -rl "gemini\|rawPrompt" --include="*.ts" src/lib/ai

# Stripe payments
grep -rl "stripe\|checkout\|webhook" --include="*.ts" src/lib/stripe src/app/api
```

### By Component Type
```bash
# Find all API routes
find src/app/api -name "route.ts"

# Find all page components
find src/app -name "page.tsx"

# Find all Server Actions
grep -rn "use server" --include="*.ts" src/

# Find all Client Components
grep -rn "use client" --include="*.tsx" src/

# Find all database queries
ls src/lib/db/*.ts

# Find all Supabase RPC calls
grep -rn "\.rpc(" --include="*.ts" src/
```

### By Error/Issue
```bash
# Find where specific error is thrown
grep -rn "CreditError\|InsufficientCredits" --include="*.ts" src/

# Find error handling patterns
grep -rn "try.*catch\|\.catch(" --include="*.ts" src/

# Find where jobs fail
grep -rn "status.*failed\|error" --include="*.ts" src/lib/db/jobs.ts

# Find R2 upload errors
grep -rn "UploadError\|presigned.*error" --include="*.ts" src/lib/r2
```

## Common Gotchas

From CLAUDE.md and project experience:

### R2 Uploads
- **Gotcha**: Browser uploads DIRECTLY to R2 via presigned URL (NOT through server)
- **Why**: Reduces server load, faster uploads
- **Flow**: Request presigned URL → Browser PUT to R2 → Confirm upload in DB

### Source Images (prompt-bench)
- **Gotcha**: Images resolved relative to batch file, hash-deduplicated in R2
- **Why**: Avoid duplicate uploads, consistent references
- **Impact**: Same source image = same R2 key across tests

### Idempotency (prompt-bench)
- **Gotcha**: Tests use hash keys to prevent duplicate evaluations on re-runs
- **Why**: Re-running tests shouldn't create duplicate database records
- **Impact**: Same template + batch = same test record

### Environment Variables
- **Gotcha**: Load `.env` BEFORE `.env.local` in prompt-bench (see `cli.ts:17-18`)
- **Why**: Base config first, then local overrides
- **Impact**: Ensures correct config precedence

### R2 Evaluation (prompt-bench)
- **Gotcha**: Evaluator fetches images from R2 URLs, not local files
- **Why**: Consistent evaluation environment
- **Impact**: Images must be uploaded to R2 before evaluation

### Dual TypeScript Configs
- **Gotcha**: `tsconfig.json` includes prompt-bench/, `tsconfig.build.json` excludes it
- **Why**: CLI/tests need prompt-bench, Next.js build doesn't
- **Impact**: Use correct config for context (Next.js uses tsconfig.build.json)

### Model Matching (pricing)
- **Gotcha**: Exact match → prefix match → error (NEVER guess provider)
- **Why**: Incorrect provider = wrong pricing
- **Impact**: Unknown models throw error, user must update `prompt-bench.toml`

## Key Tables (Supabase Schema)

| Table | Purpose | Key Columns |
|-------|---------|-------------|
| `users` | User accounts | `id`, `email`, `credits` |
| `uploads` | Source images | `id`, `user_id`, `r2_key`, `r2_url` |
| `jobs` | Generation jobs | `id`, `user_id`, `status`, `template_id` |
| `generated_images` | Output images | `id`, `job_id`, `r2_url`, `cost` |
| `credit_transactions` | Credit history | `id`, `user_id`, `amount`, `type` |
| `prompt_templates` | Template definitions | `id`, `slug`, `template`, `variables` |
| `prompt_tests` | Test runs | `id`, `template_id`, `batch_label`, `status` |
| `prompt_evaluations` | Evaluation scores | `id`, `test_id`, `image_id`, `score` |

## Deep Dives

For detailed documentation on specific subsystems, see:

- [Data Flows](reference/data-flows.md) - Detailed flow diagrams for all major operations
- [Error Patterns](reference/error-patterns.md) - Error catalog and diagnosis guides
- [Debugging Playbooks](reference/debugging-playbooks.md) - Step-by-step investigation procedures
- [Testing Guide](reference/testing-guide.md) - prompt-bench usage patterns and examples

## Commands Quick Reference

See CLAUDE.md for full command list. Most common:

```bash
# Development
bun dev                    # Start dev server
bun build                  # Production build
bun test                   # Run tests

# prompt-bench
bun bench test --template <slug> --batch <file>    # Full pipeline
bun bench evaluate --label <batch-label>           # Score images
bun bench compare --baseline <l1> --candidate <l2> # A/B testing
bun bench open-last-report                         # Open report

# Supabase
npx supabase db push       # Push schema changes
npx supabase gen types ts  # Generate TypeScript types
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/andrewske) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
