---
name: questions-answers
description: Expert assistant for the Questions & Answers (Q&A) system in Raamattu Nyt. Develop, debug, and extend the full pipeline from search query classification through question upsert, AI answer generation, community answers, voting, admin moderation, and anchor questions. Use when (1) adding features to Q&A search results, (2) modifying question classification logic, (3) working on community answer forms or moderation, (4) changing AI answer generation or prompts, (5) editing admin Q&A management pages, (6) working with anchor questions or similar questions, (7) modifying votes/upvotes/downvotes, (8) fixing Q&A-related bugs, (9) extending answer types or display, (10) working on QuestionPage, QuestionsSearchSection, or AdminQuestionsPage. Use when this capability is needed.
metadata:
  author: spectaculous-code
---

# Questions & Answers System

Full-stack Q&A pipeline: search → classify → upsert → answer (AI/community/admin) → moderate → publish.

## Architecture Overview

```
User search query
  → questionClassifier.ts (client-side)
  → upsert-question (edge fn: normalize, hash, classify, deduplicate)
  → QuestionsSearchSection renders:
      ├── Answers grouped by type (bullet → editorial → community → AI)
      ├── CommunityAnswerForm (auth-gated)
      ├── QuestionConsentBlock (AI generation / human request)
      ├── AnchorQuestionsSection (topic-linked curated Q&A)
      └── SimilarQuestionsSection (trigram similarity)
```

## Key Files

### Edge Functions
| Function | Path | Purpose |
|----------|------|---------|
| `upsert-question` | `supabase/functions/upsert-question/` | Normalize, classify, upsert, find similar |
| `generate-ai-answer` | `supabase/functions/generate-ai-answer/` | AI answer via Claude (paid, quota-checked) |
| `submit-community-answer` | `supabase/functions/submit-community-answer/` | User-submitted answer (free, needs admin approval) |
| `vote` | `supabase/functions/vote/` | +1/-1 voting on questions/answers |

### Hooks (`apps/raamattu-nyt/src/hooks/`)
| Hook | File | Purpose |
|------|------|---------|
| `useUpsertQuestion` | `useQuestions.ts` | Mutation: upsert question via edge fn |
| `useGenerateAiAnswer` | `useQuestions.ts` | Mutation: generate AI answer (quota-checked) |
| `useSubmitCommunityAnswer` | `useQuestions.ts` | Mutation: submit community answer |
| `useVote` | `useQuestions.ts` | Mutation: vote on question/answer |
| `usePublishedQuestion` | `useQuestions.ts` | Query: single published question by slug |
| `useQuestionAnswers` | `useQuestions.ts` | Query: published answers for question |
| `useAdminQuestions` | `useQuestions.ts` | Query: all questions with answer stats |
| `useAdminAnswers` | `useQuestions.ts` | Query: all answers (including unpublished) |
| `useAnchorQuestions` | `useAnchorQuestions.ts` | Query: anchor Q&A grouped by topic |

### Components (`apps/raamattu-nyt/src/components/search/`)
| Component | Purpose |
|-----------|---------|
| `QuestionsSearchSection` | Main Q&A display in search results |
| `QuestionConsentBlock` | AI answer / human request consent |
| `CommunityAnswerForm` | User answer submission form |
| `SimilarQuestionsSection` | Trigram-similar questions |
| `AnchorQuestionsSection` | Topic-linked curated Q&A |

### Pages
| Page | Route | Purpose |
|------|-------|---------|
| `QuestionPage.tsx` | `/kysymys/:slug` | Public single question view + admin tools |
| `AdminQuestionsPage.tsx` | `/ohjaamo/questions` | Full admin Q&A dashboard |
| `PremiumQuestionsPage.tsx` | `/premium/questions` | Public questions browsing |

### Core Libraries
| File | Purpose |
|------|---------|
| `src/lib/questionClassifier.ts` | Client-side question detection (Finnish) |

## Database Schema (bible_schema)

**For full schema details:** See [references/schema.md](references/schema.md)

### Tables
- **questions** — Normalized questions with classification, status, voting
- **answers** — Answers with author_type, rich content, key_verses, topics, strongs_refs
- **votes** — User votes (+1/-1) on questions/answers (unique per user)
- **topic_anchor_questions** — Links questions as anchor Q&A for topics

### Answer Author Types
| Type | Created by | Published by default? |
|------|-----------|----------------------|
| `bullet` | Admin | Yes |
| `admin` | Admin | Yes |
| `system` | Import/seed | Yes |
| `ai` | Edge function | No (needs admin review) |
| `community` | Authenticated user | No (needs admin approval) |

### Question Status Flow
```
draft → needs_review → approved → published
                    └→ rejected
```

### RLS Summary
- **Anon/Auth**: Read published questions + published non-hidden answers
- **Auth**: Read own questions + own community answers, insert votes, submit community answers
- **Admin**: Full CRUD on all questions, answers, votes

## Common Patterns

### Add new answer type
1. Add CHECK constraint value in migration
2. Update `AnswerData` interface in `QuestionsSearchSection.tsx`
3. Update `Answer` type in `useQuestions.ts`
4. Add rendering logic in `renderAnswerCard`
5. Update admin management in `AdminQuestionsPage.tsx`

### Add new question classification
1. Update classifier patterns in `questionClassifier.ts`
2. Mirror logic in `upsert-question` edge function
3. Update CHECK constraint if new DB value needed
4. Add UI handling in `QuestionsSearchSection`

### Modify AI answer generation
1. Edit prompt template via `ai-prompt-manager` skill
2. Edge function: `generate-ai-answer/index.ts`
3. Output validation: excerpt, key_verses, topics, strongs_refs, related_questions
4. Model/vendor configured via `ai_feature_bindings` table

### Add feature to community answers
1. Edge function: `submit-community-answer/index.ts`
2. Hook: `useSubmitCommunityAnswer` in `useQuestions.ts`
3. Form: `CommunityAnswerForm.tsx` (search) or inline in `QuestionPage.tsx`
4. Admin tab: "Yhteisövastaukset" in `AdminQuestionsPage.tsx`

## Shared UI Components

- **BibleReferencePickerPopover** — Verse picker popover (used across 6 files)
- **SafeTextarea** — Input with XSS protection, markup detection
- **RichContentRenderer** — Renders body_html from rich editor
- **VerseContentCard** — Displays verse reference with content
- **FeedbackThumbs / FeedbackText** — Answer feedback (AI answers)

## References

- **Full schema + RLS policies**: See [references/schema.md](references/schema.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spectaculous-code) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
