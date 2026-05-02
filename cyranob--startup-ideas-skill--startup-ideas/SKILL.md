---
name: startup-ideas
description: Interactive startup ideation and planning pipeline that walks users through generating business ideas, target personas, elevator pitch, customer avatar, diary entry, feature list, pricing strategy, and landing page outline. Use when the user wants to brainstorm startup ideas, develop a product concept, plan a new business, create a go-to-market strategy, or work through any stage of early-stage startup planning. Also triggers on requests like "help me come up with a business idea," "I need a startup plan," "generate a pitch for my idea," or "help me think through my product. Use when this capability is needed.
metadata:
  author: cyranob
---

# Startup Ideas

Interactive 8-step startup ideation pipeline. Walk the user through each stage, collecting input between steps.

## Workflow

Read [references/prompts.md](references/prompts.md) before generating any content. It contains the output formats and domain guidance for each stage.

### Pipeline stages

1. **Generate Ideas** -- Produce 4 business concepts based on user's focus/domain
2. **Personas** -- Create 2 target audience personas for the selected idea
3. **Pitch** -- Write elevator pitch, problem statement, target audience, and USP
4. **Avatar** -- Build a detailed problem-aware customer avatar (A-H framework)
5. **Diary** -- Write a first-person diary entry from the avatar's perspective
6. **Features** -- Generate avatar-aligned feature list
7. **Pricing** -- Develop a pricing strategy (single model, single metric)
8. **Landing Page Outline** -- Produce a section-by-section landing page outline

### Interaction protocol

At each stage:
1. Generate the content using the format from prompts.md
2. Present it to the user
3. Ask if they want to **continue**, **regenerate**, or **edit/adjust** before moving on
4. Carry forward all prior context to the next stage

### Stage 1: Generate Ideas

Ask the user:
- What domain or problem space interests them (or "surprise me" for random)
- Any constraints (solo founder, budget, target market, business model preference)

Generate 4 ideas. Present them numbered. Ask the user to **pick one** (or request new ideas).

### Stage 2: Personas

Using the selected idea, generate 2 personas (Primary + Secondary) following the structured format in prompts.md. Present them as readable cards.

### Stage 3: Pitch

Using the idea + personas, generate the pitch components. Present each section clearly.

### Stage 4: Avatar

Using idea + personas + pitch, build one detailed customer avatar using the A-H framework from prompts.md. This is the most detailed stage -- present each section (A through H) clearly.

### Stage 5: Diary

Using the avatar, write a 140-220 word first-person diary entry. The diary must be:
- Emotionally specific and solution-agnostic (no product mentions)
- Grounded in the avatar's voice, pains, and daily reality
- Rich with sensory detail and concrete moments

### Stage 6: Features

Using idea + avatar, produce a markdown bullet list of avatar-aligned features. Each bullet: feature name + short benefit clause.

### Stage 7: Pricing

Using idea + features + avatar, create a pricing strategy. Follow the KISS rules: one model, one metric, max 3 tiers.

### Stage 8: Landing Page Outline

Using all prior context (idea, features, avatar, diary, pricing), produce a section-by-section landing page outline. Benefit-first, CRO-focused.

### Entry points

Users may enter at any stage:
- **"I have an idea already"** -- Skip to Stage 2 (or any later stage) with user's existing idea
- **"Help me with pricing"** -- Ask for enough context (idea, features, target user) then jump to Stage 7
- **Partial context** -- Ask only for what's missing to complete the requested stage

### Output format

Present all generated content in well-structured markdown. For the complete pipeline, offer to compile everything into a single summary document at the end.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cyranob) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
