---
name: tacosdedatos-pipeline
description: | Use when this capability is needed.
metadata:
  author: chekos
---

# tacosdedatos Content Pipeline

Transform a content idea into a complete publication package with coordinated AI agents.

## When to Use This Skill

Use when starting a new piece of content:
- "I want to write about DuckDB for local data analysis"
- "Let's create a tutorial on Polars vs Pandas"
- "I have an idea for a newsletter about AI agents"

**Not for:**
- Quick social posts (use `content-to-social` directly)
- Editing existing content (use `tacosdedatos-editor`)
- Just generating social posts (use platform-specific skills)

## The Pipeline

```
┌─────────────────────────────────────────────────────────────────┐
│                    TACOSDEDATOS PIPELINE                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  PHASE 1: IDEATION                                              │
│  ┌─────────────┐                                                │
│  │ Content Idea │ ──→ Pitch Evaluation ──→ [CHECKPOINT 1]      │
│  └─────────────┘      (beat-reporter)       Human approves      │
│                                              pitch              │
│                                                                 │
│  PHASE 2: CREATION                                              │
│  ┌─────────────┐                                                │
│  │   Research   │ ──→ Draft ──→ [CHECKPOINT 2]                 │
│  └─────────────┘               Human reviews                    │
│                                draft                            │
│                                                                 │
│  PHASE 3: REFINEMENT                                            │
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐         │
│  │ Fact-Check  │ ──→│ Copy Edit   │ ──→│ Voice Check │         │
│  │(fact-checker)│   │(copy-editor)│    │(tdd-editor) │         │
│  └─────────────┘    └─────────────┘    └─────────────┘         │
│                                              ↓                  │
│                                        [CHECKPOINT 3]           │
│                                        Human approves           │
│                                        final draft              │
│                                                                 │
│  PHASE 4: OPTIMIZATION                                          │
│  ┌─────────────┐    ┌─────────────┐                            │
│  │     SEO     │ ──→│Subject Line │                            │
│  │(tacosdedatos│    │ (subject-   │                            │
│  │    -seo)    │    │  line-opt)  │                            │
│  └─────────────┘    └─────────────┘                            │
│                                                                 │
│  PHASE 5: DISTRIBUTION                                          │
│  ┌─────────────────────────────────────────────────┐           │
│  │              content-to-social                   │           │
│  │  ┌───────┐ ┌────────┐ ┌─────────┐ ┌─────────┐  │           │
│  │  │X/Tweet│ │LinkedIn│ │Instagram│ │ Bluesky │  │           │
│  │  └───────┘ └────────┘ └─────────┘ └─────────┘  │           │
│  └─────────────────────────────────────────────────┘           │
│                            ↓                                    │
│                      [CHECKPOINT 4]                             │
│                      Human approves                             │
│                      publication                                │
│                                                                 │
│  OUTPUT: Complete Publication Package                           │
│  ├── article.md (final draft)                                   │
│  ├── meta.yaml (SEO metadata)                                   │
│  ├── subject-lines.md (newsletter options)                      │
│  └── social-bundle.md (all platform posts)                      │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

## Human Checkpoints

The pipeline pauses at 4 key moments for human judgment:

| Checkpoint | What's Reviewed | Typical Time |
|------------|-----------------|--------------|
| **1. Pitch** | Is this worth writing? Angle correct? | 2-3 min |
| **2. Draft** | Structure, depth, direction | 10-15 min |
| **3. Final** | Voice, accuracy, quality | 5-10 min |
| **4. Publish** | Everything ready to go live? | 2-3 min |

**Total human time:** ~20-30 minutes per article

## Phase 1: Ideation

### Input
```
User: "I want to write about [topic]"
```

### Process

1. **Expand the idea** into a pitch using `beat-reporter` agent patterns:
   - What's the core insight?
   - Why does this matter now?
   - Who is the audience?
   - What's the hook?

2. **Evaluate the pitch** against tacosdedatos criteria:
   - Does it fit our content pillars? (Educational 40%, Community 25%, BTS 20%, Promo 15%)
   - Is there a unique angle?
   - Can we add genuine value?

3. **Structure recommendation**:
   - Transformation Arc (personal journey)
   - Deep Dive (technical tutorial)
   - Reflective (industry commentary)

### Output
```markdown
## Pitch: [Title]

**Hook**: [Opening line]
**Thesis**: [Main argument]
**Structure**: [Recommended format]
**Estimated length**: [Word count]
**Content pillar**: [Category]

**Why now**: [Timeliness]
**Unique angle**: [What makes this different]
```

### Checkpoint 1
Present pitch to human. Options:
- ✅ Approve and continue
- 🔄 Revise angle/approach
- ❌ Reject (not right for tacosdedatos)

## Phase 2: Creation

### Process

1. **Research** the topic:
   - Find authoritative sources
   - Identify code examples
   - Gather statistics/data
   - Note competing articles

2. **Draft** the article:
   - Follow the approved structure
   - Apply tacosdedatos voice (see `tacosdedatos-editor` skill)
   - Use hook formulas (see `references/hook-formulas.md`)
   - Include code examples with comments
   - Add personal anecdotes where relevant

3. **Self-review** against checklist:
   - Opening follows 4-beat rhythm?
   - Each section concrete → abstract?
   - Visual breaks every 300-400 words?
   - Closing reframes (not recaps)?

### Output
```markdown
# [Article Title]

[Full draft content]

---

## Draft Metadata

**Word count**: [X]
**Reading time**: [X min]
**Code examples**: [X]
**Structure**: [Type]
**Sources**: [List]
```

### Checkpoint 2
Present draft to human. Options:
- ✅ Approve for editing
- 🔄 Request revisions (specify what)
- 🔙 Return to pitch phase

## Phase 3: Refinement

### Process (Sequential)

1. **Fact-Check** (`fact-checker` agent):
   - Verify all claims
   - Check code examples work
   - Validate statistics
   - Flag uncertain statements

2. **Copy Edit** (`copy-editor` agent):
   - Grammar and spelling
   - Sentence flow
   - Paragraph transitions
   - Formatting consistency

3. **Voice Check** (`tacosdedatos-editor` skill):
   - Authentic voice?
   - Anti-AI markers cleared?
   - Cultural grounding present?
   - Coffee test passed?

### Output
```markdown
# [Article Title] - FINAL

[Polished content]

---

## Refinement Report

### Fact-Check
- [X] All claims verified
- [X] Code examples tested
- ⚠️ [Any flags]

### Copy Edit
- Fixed: [X] issues
- Style: [Notes]

### Voice Check
- Authenticity: [Strong/Adequate/Needs work]
- Notes: [Specific feedback]
```

### Checkpoint 3
Present final draft to human. Options:
- ✅ Approve for publication prep
- 🔄 Request specific changes
- 🔙 Return to draft phase

## Phase 4: Optimization

### Process (Parallel)

1. **SEO Optimization** (`tacosdedatos-seo` skill):
   - Meta title and description
   - Keyword optimization
   - Internal linking opportunities
   - Schema markup recommendations

2. **Subject Line Generation** (`subject-line-optimizer` skill):
   - 5-10 variants
   - Different patterns (question, contrarian, value, etc.)
   - Top 3 recommendations

### Output
```yaml
# meta.yaml
title: "[SEO title]"
description: "[Meta description]"
keywords: ["keyword1", "keyword2"]
canonical: "[URL]"
og_image: "[Image URL or description]"

subject_lines:
  recommended:
    - "[Option 1]"
    - "[Option 2]"
    - "[Option 3]"
  all_variants:
    - pattern: "question"
      line: "[...]"
    - pattern: "contrarian"
      line: "[...]"
```

## Phase 5: Distribution

### Process

Use `content-to-social` skill to generate platform-specific posts:

1. **Extract core elements** from article
2. **Generate** for each platform:
   - X/Twitter (long-form or thread)
   - LinkedIn (story format)
   - Instagram (carousel concept)
   - Bluesky (authentic thread)

### Output
See `content-to-social` skill for full bundle format.

### Checkpoint 4
Present complete package to human:
- Final article
- SEO metadata
- Subject line options
- Social media bundle

Options:
- ✅ Approve all — schedule publication
- 🔄 Revise specific element
- ⏸️ Hold for timing

## Complete Output Package

```
publication-package/
├── article.md          # Final article
├── meta.yaml           # SEO metadata + subject lines
├── social-bundle.md    # All platform posts
├── assets/
│   ├── header.md       # Image prompt/description
│   └── diagrams/       # Any visual assets
└── pipeline-log.md     # Record of all phases
```

## Pipeline Log Format

Track the journey:

```markdown
# Pipeline Log: [Article Title]

## Timeline
- **Started**: [Date/time]
- **Pitch approved**: [Date/time]
- **Draft approved**: [Date/time]
- **Final approved**: [Date/time]
- **Published**: [Date/time]

## Checkpoints

### Checkpoint 1: Pitch
- **Decision**: Approved
- **Notes**: [Any feedback]

### Checkpoint 2: Draft
- **Decision**: Approved with revisions
- **Revisions requested**: [What changed]

### Checkpoint 3: Final
- **Decision**: Approved
- **Notes**: [Any final feedback]

### Checkpoint 4: Publication
- **Decision**: Approved
- **Publish date**: [Scheduled date]
- **Platforms**: [List]

## Metrics (Post-Publication)
- **Newsletter opens**: [X%]
- **Article views**: [X]
- **Social engagement**: [Summary]
- **Notes**: [Learnings for future]
```

## Skill Dependencies

This pipeline orchestrates:

| Phase | Skill/Agent | Plugin |
|-------|-------------|--------|
| Ideation | `beat-reporter` agent | tdd-editor |
| Creation | `tacosdedatos-writer` patterns | tdd-editor |
| Fact-Check | `fact-checker` agent | tdd-editor |
| Copy Edit | `copy-editor` agent | tdd-editor |
| Voice Check | `tacosdedatos-editor` skill | tdd-editor |
| SEO | `tacosdedatos-seo` skill | tdd-growth |
| Subject Lines | `subject-line-optimizer` skill | tdd-growth |
| Social | `content-to-social` skill | tdd-growth |

## Quick Start

```
User: "Create a tacosdedatos article about using DuckDB for local analytics"

Pipeline will:
1. Generate pitch → wait for approval
2. Research and draft → wait for approval
3. Fact-check, copy-edit, voice-check → wait for approval
4. Generate SEO + subject lines + social bundle → wait for approval
5. Output complete publication package
```

## Configuration Options

Customize the pipeline:

```yaml
# Optional: Skip phases for experienced workflows
skip_pitch: false        # Skip pitch phase (you know what you want)
skip_fact_check: false   # Skip for opinion pieces
auto_approve_seo: true   # Don't checkpoint SEO phase

# Optional: Platform selection
platforms:
  - twitter
  - linkedin
  # - instagram  # Skip if no visuals
  - bluesky

# Optional: Content type presets
preset: "tutorial"  # tutorial | essay | news | roundup
```

## Anti-Patterns

Avoid these pipeline mistakes:

| Mistake | Problem | Solution |
|---------|---------|----------|
| Skipping checkpoints | Quality issues slip through | Always pause for human review |
| Rushing the pitch | Wrong direction wastes time | Spend time on pitch approval |
| Ignoring voice check | Content sounds generic | Voice check is non-negotiable |
| Publishing without social | Missed distribution | Always generate social bundle |
| No pipeline log | Can't learn from process | Always maintain the log |

## Success Metrics

Track pipeline health:

| Metric | Target |
|--------|--------|
| Pitch → Publish time | < 4 hours |
| Human review time | < 30 min total |
| Revision rounds | ≤ 2 per checkpoint |
| Voice check pass rate | > 80% first try |
| Social posts generated | 4+ platforms |

## Related Skills

- `tacosdedatos-editor` — Detailed editorial review
- `tacosdedatos-writer` — Content creation patterns
- `content-to-social` — Cross-platform distribution
- `subject-line-optimizer` — Newsletter subject lines
- `tacosdedatos-seo` — SEO optimization

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chekos) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
